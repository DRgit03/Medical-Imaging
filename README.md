# Medical-Imaging
Medical imaging for pneumonia classification , heart attack detection and liver tumor segmentation.   Converting images from Dicom files to NIFTI

What is DICOM?
DICOM-> Digital imaging and communications in medicine.
Is the international standard for medical images and related information. It defines for formats for medical images that can be exchanged with data and  quality necessary for clinical use.

What is DICOM used for?
	Store and share medical images(..and other data)
	Communication between  medical imaging devices
	Most medical image data in hospitals are stored in the DICOM format

Structure:
	Header: The  header contain all relavant information about the procedure taken, including, but not limited to  the device information
o	Device information (Which modality / what scanner)
o	Patient information
o	Study UID & series UID to able to map and scan to a particular patient and all relevant image information such as shape or thickness.
o	Image information (shape, slice thickness)
o	And many more
	Body
o	Actual image pixel data (2D, 3D, 4D,…)

The DICOM Header: Each entry in a DICOM header is accessible by its unique data tag. This allows for automatic extraction of desired pieces of information like as an age of the patient. All header entries follow the same structure 
Example: (0018, 0015) Body part examined -> CS: “CHEST”(Tag->Explanation -> value).
Unfortunately, because Dicom is such a complex standard, it can be confusing.
…DICOM can be confusing:
	Vendor- specific adaptations (eg. “private tags” in header)
	3D volumes often stored as multiple 2D DICOM files
	DICOM file extension variable (“.dcm”, “.ima”, sometimes none).
Thus mainly in a clinical setting and often requires conversion to other formats for research / ML.

How to work with DICOM files
	Python libraries: PyDicom, SimpleTK
	Image viewer: 3D slicer (www.slicer.org)
Protecting Patient privacy : If you are working in a clinical setting, you absolutely have to anonymize you Dicom files before publishing them to protect the rights and privacy of the patient.
Often anonymization required(www.mircwiki.rsna.org).
Dicom file contains extremely sensible information about patient.


Neuroimaging Informatics Technology Initiative (NIfTI):
What is NIfTI?
NIfTI : Neuroimaging Informatics Technology Initiative .
	An open file format for storage of medical image data (historically used for neuroimaging -hence the name, but not restricted to neuroimaging)

What is NIfTI used for?
	Efficiently store medical image data together with necessary metadata.
	Mainly used in research settings
	Not a clinical standard.

Structure:
Header: 
	Containing information mainly about image geometry (resolution, position, orientation)
Body:
	Actual image pixel data (2D, 3D, 4D…)
Please note that as it contains the full volume when dealing with 3 or 4 dimensional data, we only have to load a single file, thus making it general easier to handle than  plain Dicom files. In general, easier to handle than DICOM files
File extension usually “.nii” or  “.nii-gz”(compressed version).

How to work with NIfTI files
	Python libraries: NiBabel, simpleTK
	Image viewer: 3D slicer (www.slicer.org)
	Transform DICOM to NIfTI in python: dicom2nifti




IMAGE PREPROCESSING:
Why Preprocessing?
	Data need to be prepared for training and testing
	Modality specific pre-process steps.
	Normalization and Standardization.
	Homogenization of image resolution / size / orientation etc
	Some Machine learning methods may require specified input formats ( e.g. images of certain size)
	Faster access of data during training

Preprocessing steps used in the field of medical imaging:
1.	Modality specific pre-processing.
2.	Orientation
3.	Spatial resampling / Resizing
4.	Intensity normalization and standardization
5.	Conversion to algorithm input format.

	Modality Specific Preprocessing: 
1.	Image reconstruction algorithm (MR, CT, PET): deals with the task of finding suitable processing techniques for all modalities , even image reconstruction algorithms which are supposed to convert the raw output of the scanner into a form that humans can actually understand is a part of those techiniques. How ever usually those algorithms are directly implemented in the scanner and you don’t have to deal  with.

	Denoising: Noise removal from either the raw or the already reconstructed scanner outputs.
There are some rare occasions where you want to apply denoising algorithms yourselves but  most of the time the data we deal with as developers is already denoised .

	Complex artefact correction (e.g. motion, bias-field, …): Depending upon the modality you work with, complex artefacts might occur.Especially motion is a much harder problem when working with CT or MRI data, but not that bad when working with ultrasound. The task of motion correction has its own research field. Sometimes motion correction might already be implemented in the scanner, but not all time. If you have to work with data corrupted by motion, you should absolutely take a look at the current  results and algorithms used in this field of research.
	Quatification (standard units) : Is to make sure that all outputs have the same unit. Most of the time this techniques is directly implemented in the scanner. However , depending on the task and modality as an example , when working with pet images, it might be possible that you have to manually quantify your dataset.


2.	Orientation:
	Goal: The goal of orientation based pre-processing algorithms is to achieve Standardized image orientation for the whole dataset.
	Data array axis should always correspond to the same physical dimensions.
	Example, if you miss to handle this step, you might obtain a dataset where the first axis sometimes moves from top to bottom and some time moves from left to right. Such dataset will absolutely decrease your training performance and drastically worsen your results.

3.	Resampling
	When performing resampling our overall goal is to get a standardized physical resolution and size. This means that all your scans in the dataset have the same shape and all voxels in all scans have the same physical resolution or dimensions -> Resampling, padding/Cropping.
	. Most of the time used to reduce the size of the volume( eg from  512 * 512 *  512 to 128 * 128 * 128). For example , each voxel might cover an area of one cross, one millimetre. This can be performed by either resampling or if the single voxels already have the same physical resolution.
4.	Normalization and Standardization:
	Goal: Standardization of intensity values.
	Modality specific standardization to defined values intervals (e.g.[-1,1])
	Normalization & Standard per subject (e.g. MR)
	Standardization per dataset(eg.CT): it is one of the most important preprocessing step for machine learning developers.  The goal of this process is to get you pixel values into specific intervals. As an example between -1 to 1 or 0 to 1 , this procedure can simplify the learning process  for the model and may increase the training , speed , the performance of the model, or even both depending on the task at hand you need to use different normalization or standardization techiniques.
	As an example, when dealing with MRI data, you set normalize and standardize per subject. However, in contrast, when working with CT scans, you typically do not need to normalize but standardize complete dataset for highly specific tasks. 
	There are also some advance methods as an example , when working with pet images, you might want to compute the logarithm of the single  voxel values.
	Most of the time when working with brain images you match your individual brain scan onto the MNI template. You can imagine the template as a cod or an atlas of the brain. This template matching approach only works because the brain is extremely similar within all individuals and highly symmetric.
	The following table contains an overview over the most common modalities and their pre-processing techiniques.
o	When working with X-ray images , you at first standardize your data by scaling those images with a constant of one divided by 255. After that you compute the mean and standard deviation of the whole dataset to perform set normalization. When dealing with CT images, you typically don’t need to perform normalization. How ever it might increase performance when you scale the data with the constant one divided by 3071.

