# features_extractor_2Dpdf
Extract features from a technical definition provided as *.pdf 2D model  

One [technical_drawing.pdf](./samples/sample1.pdf) document is used to generate a vector of N features.  

This features extractor is used a preliminary step to feed a machine learning model described [here](https://github.com/BruSunshine/predict_manufacturing_costs/README.md)   

**Format**  
This files is a pdf \*.pdf format as the most used format in this field but it could also be an image of any type.   

**Comment**   

Each offer is made for a given part which is described by a specific pdf drawing of one or several pages.  
Each technical drawing contains several 2D projection of the part in several positions.  
The main objective of this drawing is to display the tolerances and other important information for the manufacturer.  
It is generated from a 3D CAD program, for the shapes and the main dimensionning elements.  
All tolerances are added manually when generating the detailed 2D drawing.  
Usually the 3D files does not include specific details or patterns like threads, which have a cost impact.
This is why the exctration of all possible features representing part complexity and tolerances is important here.  

We performs this with using OCR technics.

The technical drawings conventions are strict and well followed, which should allow us to detect patterns, encodings, numbers sufficiently.

We plan to use Google Vision API to perform the texte detection in images transformed from pdf by a library called pdf2image.
The choice of pdf2image just because a simple function to perform this.
The choice of google vision 'text detection' rather than google vision 'document text detection' because the texts are not grouped in paragraphs in the images, they are positionned all around, with different types of orientations. As a first step and to limit the scope of this specific project: We would not apply specific pre-processing step (with for instance PIL or OpenCV) although we know it could help improving detection. We saw on few images that google vision seems to perform better on recognizing the floats and some specific patterns of the technical drawing in comparision to tesseract v4.0, and tesseract would also need preprocessing.  

The recognition of the floats in the image is the easiest part and we can generate a list with all those floats which we turn in precious features:  
in particular we are interested in getting at least as much as possible the tolerancing informations: for this reason we look at the density of digits extracted in several orders of magnitude: We define 2 groups which correspond to the dimension on one side and to the tolerances associated on the other side. Dimensions are typically between 0.001mm and 0.5mm, and dimensions between 1mm and 600mm.  

We are unsure about the capability of google vision API to detect all unicodes symboles related to mechanical drawings we would like to detect. For the time beeing the API seems to recognize well the diameter symbol only. We believe we could train the google vision model with the feature called label detection to detect around 20 tags (specific unicodes symboles) constantly used in the drawings.  

For further feature extraction, the difficulty comes in the positionning of informations, which varies completely from one drawing to the other. For specific tolerancing element, a piece of information is a pattern in relation with a float, for a tolerance class, it will be a norm in relation to a string, etc.. 

The drawing also contains some less structured and codified informations which are more difficult to retrieve such as the presence of heat treatments or paintings, we will try to detect and add to the features list as a boolean indicator (presence of heat treatment, presence of painting, presence of surface treatment etc..).  

We would also like to add an indicator on the drawing quality: as we will see later in the the section related to missing information, we have a different level of success in the detection of elements depending on the drawing quality. Mainly we see 2 types: the modern pdf drawing style with good detection level, and the old pdf scanned again and again, including hand written digits and eventually noise, with less good detection level. We think it is a good idea to implement an indicator which will distingish between the 2 groups, in order to inform the ML model about this effect.