PRINTING ERROR DATASET by Nils Beyer, 30.01.2023

The labels in the dataset are explained in the labels.txt

Overview of the PRINTING ERROR DATASET structure:

 ├── general data
     └── all_images_no_filter.csv			# Full Dataset, unfiltered
     └── all_images.csv					# Full Dataset, no spaghetti error
     └── black_bed_all.csv				# Full Dataset, no silver bed

 
 ├── images					   
     └── all_images
     |		└── ... 				# All Images: Full Dataset + Silver Bed + Oblique Camera
     |
     └── test_images_silver265
     | 		└── ... 				# Silver bed test images
     |
     └── test_images_oblique256
         	└── ...					# Oblique camera test images
 
