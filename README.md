# Anime Style Illustration Specific Image Search App with ViT Tagger x LSI
## What's This?
- Anime Style Illustration Specific Image Search App with ML Technique
  - can be used for photos. but flexible photo search is offered by Google Photos or etc :)
- Search capabilities of cloud photo album services towards illustration image files are poor for some reason
- So, I wrote simple scripts

## Method
- Search Images matching with Query Texts on Latent Semantic Representation Vector Space
  - Vectors are generated with embedding model: Visual Transformar (ViT) Tagger x Latent Semantic Indexing (LSI) 
- LSI is used for Covering Tagging Presision
  - You can use tags to search which are difficult for tagging because the index data which is composed of vectors is applyed LSI
  - implemented with Gensim lib
- ( Web UI is implemented with StreamLit )

## Usage
- $ pip install -r requirements.txt
- $ python make-tags-with-wd-tagger.py --dir "IMAGE FILES CONTAINED DIR PATH"
  - The script searches directory structure recursively :)
  - This takes quite a while...
    - About 0.5 sec/file at middle spec desktop PC (GPU is not used)
      - AMD Ryzen 7 5700X 8-Core Processor 4.50 GHz
    - You may speed up with editing the script to use CUDAExecutionProvider, CoreMLExecutionProvider and etc :)
      - Plese see [here](https://onnxruntime.ai/docs/execution-providers/)
      - Performance key is processing speed of ONNX Runtime at your machine :)
  - Image files and tags of these are saved to tags-wd-tagger.txt
- $ python count-unique-tag-num.py
  - => for deciding appropriate dimension scale fitting your data
- $ python gen-lsi-model.py
  - **Please edit [num_topics paramater](https://github.com/ryogrid/local-illust-image-searcher/blob/main/gen-lsi-model.py#L51) before execution**
  - I think about 80% of unique tags which is counted with count-unique-tag-num.py is better
    - EX: unique tags count is 1000 -> 0.8 * 1000 -> 800 num_topics (dimension)
  - This takes quite a while...
    - LSI processing: dimension reduction with [Singular Value Decomposition (SVD)](https://en.wikipedia.org/wiki/Singular_value_decomposition)
    - Take several secs only for 1000 files and reduction from 800 to 700 dimension case (case of demo on later section)
    - But, in 330k files and from 8000 to 5000 dimension case, about 1 hour is taken
      - files are not for demo :)
- $ streamlit run web-ui-image-search-lsi.py
  - Search app is opend on your web browser

## Tips (Attention)
- Words (tags) which were not apeeared at tagging are not usable on query
  - Solution
    - Search words you want to use from taggs-wd-tagger.txt with grep, editor or something for existance checking
    - If exist, there is no problem. If not, you should think similar words and search it in same manner :) 
- Character code of file pathes 
  - If file path contains charactors which can't be contered to Unicode or utf-8, scripts may ouput error message at processing the file
  - But, it doesn't mean that your script usage is wrong. Though these files is ignored or not displayed at Web UI :|
    - This is problem of current implentation. When you use scripts on Windows and charactor code of directory/file names isn't utf-8, the problem may occur

## Information Related to Copylights
- I used [this code](https://huggingface.co/spaces/SmilingWolf/wd-tagger/blob/main/app.py) as reference wheh implmenting tagger script
- ["WD ViT Tagger v3" model](https://huggingface.co/SmilingWolf/wd-vit-tagger-v3) is used for image file tagging
- **I thank to great works of SmilingWolf**

## TODO
- [ ] <del>Search on latent representation generated by CLIP model</del>
  - This was alredy tried but precition was not good because current public available CLIP models are not fitting for anime style illust :|
- [ ] Weight specifying to keyword like prompt format of Stable Diffusion Web UI
  - Current implemenataion uses all keywords faialy. But there is many cases that users want to emphasize specific keyword and can't get appropriate results without that!
- [ ] Incremental index updating at image files increasing
- [ ] Similar image search with specifying target image file 
- [ ] Exporting found files list feature
  - In text file. Once you get list, many other tools and viewer you like can be used :)
- [ ] Making binary package of this app which doesn't need python environment building


## Screenshots of Demo
- I used about 1000 image files collected from [Irasutoya](https://www.irasutoya.com/) which offers free image materials as search target example
  - Note: image materials of Irasutoya have restrictions at commercial purposes use
- Partial tagging result: [./tagging_example.txt](/tagging_example.txt)
  - Generation script was executed in Windows
  - File paths in linked file have been partially masked 


- Search "standing"
  - ![image](https://github.com/user-attachments/assets/6e324a1e-ae49-40de-9dbd-d040e153b837)
- Search "standing animal"
  - ![image](https://github.com/user-attachments/assets/cd2862e3-e7e2-42fe-b830-705c778e10b8)
- Image info page
  - ![image](https://github.com/user-attachments/assets/78898162-ac6d-4fdf-b806-798f2f52a8d5)
- Slideshow feature
  - Auto slide in 5 sec period (roop)
  - ![image](https://github.com/user-attachments/assets/ea42336f-6b59-402b-b19e-f10610e4b200)
  



