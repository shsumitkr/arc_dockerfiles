# sudo docker container run -u $(id -u):$(id -g) --runtime=nvidia -it --rm -v $PWD:/workspace -w /workspace <image>:<tag> python

FROM tensorflow/tensorflow:2.1.1-gpu
MAINTAINER GnosisLab

ENV LEPTONICA_V=1.79.0 TESSERACT_V=4.1.1 OPENCV_V=4.3.0 MUPDF_V=1.17.0

RUN apt-get update && apt-get install -y build-essential wget \
  cmake pkg-config libtool automake libtbb2 \
  zlib1g-dev libfreetype6-dev libharfbuzz-dev libjbig2dec0-dev \
  libswscale-dev libjpeg-dev libpng-dev libopenjp2-7-dev libtiff-dev wget \
  && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade jupyter sympy nose matplotlib seaborn \
      statsmodels pandas Pillow scipy scikit-image scikit-learn

RUN wget -c http://www.leptonica.org/source/leptonica-$LEPTONICA_V.tar.gz -O - | tar xz && \
  cd leptonica-$LEPTONICA_V && ./configure && make -j`nproc` && make install && ldconfig && \
  cd .. && rm -rf leptonica-*

RUN wget -c https://github.com/tesseract-ocr/tesseract/archive/$TESSERACT_V.tar.gz -O - | tar xz && \
  cd tesseract-$TESSERACT_V && ./autogen.sh && ./configure --prefix=/usr  && \
  make -j`nproc` && make install && ldconfig && \
  cd .. && rm -rf tesseract-*

RUN wget -c https://github.com/opencv/opencv/archive/$OPENCV_V.tar.gz -O - | tar xz && \
  cd opencv-$OPENCV_V && mkdir build && cd build && \
  cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr -D OPENCV_GENERATE_PKGCONFIG=ON .. && \
  make -j`nproc` && make install && ldconfig && \
  cd .. && cd .. && rm -rf opencv

RUN wget -c https://www.mupdf.com/downloads/archive/mupdf-$MUPDF_V-source.tar.gz -O - | tar xz && \
  cd mupdf-$MUPDF_V-source/ && make HAVE_X11=no HAVE_GLUT=no prefix=/usr/local install && \
  cd .. && rm -rf mupdf-*

ENV TESSDATA_PREFIX /usr/share/tessdata
ADD https://github.com/tesseract-ocr/tessdata/raw/master/eng.traineddata ${TESSDATA_PREFIX}/
ADD https://github.com/tesseract-ocr/tessdata/raw/master/osd.traineddata ${TESSDATA_PREFIX}/
RUN chmod -R 755 /usr/share/tessdata
