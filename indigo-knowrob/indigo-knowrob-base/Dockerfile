FROM ubuntu:14.04
MAINTAINER Ferenc Balint-Benczedi

RUN apt-get -qq update && apt-get -qq install -y wget apt-utils

# Use apt-cacher container
RUN echo 'Acquire::http { Proxy "http://172.17.42.1:3142"; };' >> /etc/apt/apt.conf.d/01proxy

# Install ROS
RUN sh -c '. /etc/lsb-release && echo "deb http://packages.ros.org.ros.informatik.uni-freiburg.de/ros/ubuntu trusty main" > /etc/apt/sources.list.d/ros-latest.list'
RUN wget http://packages.ros.org/ros.key -O - | apt-key add - && \
    apt-get -y update && apt-get -y install \
                libjson-java \
                libjson-glib-dev \
                ros-indigo-desktop \
                swi-prolog \
                swi-prolog-java \
                openjdk-7-jdk \
                python-yaml \
                python-catkin-pkg \
                python-twisted-web \
                python-twisted-bin \
                python-twisted-core \ 
                python-rospkg \
                python-wstool \
                ros-indigo-catkin \
                ros-indigo-iai-common-msgs \
                ros-indigo-rosjava \
                ros-indigo-rosbridge-suite \
                ros-indigo-tf2-web-republisher \
                ros-indigo-mjpeg-server \
                ros-indigo-web-video-server \
                ros-indigo-self-test \
                ros-indigo-compressed-depth-image-transport \
                ros-indigo-compressed-image-transport \
                ros-indigo-image-transport \
                ros-indigo-pcl-ros \
                ros-indigo-pcl-conversions \
		        ros-indigo-vision-opencv \            
         		ros-indigo-openni-camera \
                ros-indigo-openni-launch \
                mongodb-clients \
                mongodb-dev \
                libmongo-client-dev \
                ros-indigo-rosauth \
                mencoder \
                lame \
                libavcodec54 \
                texlive-latex-base \
                imagemagick \
                automake \
                libxerces-c-dev \
                libicu-dev \
                libapr1-dev \
                libgphoto2-2-dev \
                libhdf5-serial-dev \
                protobuf-compiler \
                libopencv-dev \
                libcppnetlib-dev \
                scons \
                libtool \
                xstow \
                vim \
                emacs \
                gdb \
                cmake-curses-gui \
                git


# create user 'ros'
RUN useradd -m -d /home/ros -p ros ros && adduser ros sudo && chsh -s /bin/bash ros
ENV HOME /home/ros

# ROS environment setup
RUN cp /opt/ros/indigo/setup.sh /etc/profile.d/ros_indigo.sh && rosdep init

WORKDIR /home/ros
USER ros
RUN mkdir /home/ros/src && chown -R ros:ros /home/ros && rosdep update && \
	  mkdir -p /home/ros/.m2

ADD mvn-settings.xml /home/ros/.m2/settings.xml

ENV CMAKE_PREFIX_PATH /home/ros/devel:/opt/ros/indigo
ENV PKG_CONFIG_PATH=/home/ros/local/lib/pkgconfig:/home/ros/devel/lib/pkgconfig:/home/ros/devel/lib/x86_64-linux-gnu/pkgconfig:/opt/ros/indigo/lib/pkgconfig:/opt/ros/indigo/lib/x86_64-linux-gnu/pkgconfig
ENV PYTHONPATH /home/ros/local/lib/python/site-packages:/home/ros/devel/lib/python2.7/dist-packages:/opt/ros/indigo/lib/python2.7/dist-packages

WORKDIR /home/ros
RUN mkdir -p /home/ros/local/src && mkdir -p /home/ros/local/xstow
WORKDIR /home/ros/local/src
RUN git clone https://github.com/RoboSherlock/uima-uimacpp.git uimacpp && \
    git clone https://github.com/mongodb/mongo-cxx-driver.git -b 26compat
WORKDIR /home/ros/local/src/uimacpp
RUN ./autogen.sh && ./configure --without-activemq --with-jdk=/usr/lib/jvm/java-7-openjdk-amd64/include --prefix=/home/ros/local/xstow/uimacpp --with-icu=/usr && make install 
WORKDIR /home/ros/local/src/mongo-cxx-driver
RUN scons --full --use-system-boost --prefix=/home/ros/local/xstow/mongoclient --ssl --sharedclient install-mongoclient
WORKDIR /home/ros/local/xstow
RUN xstow uimacpp && xstow mongoclient


RUN echo "export LD_LIBRARY_PATH=/home/ros/local/lib:/usr/lib/jvm/default-java/jre/lib/amd64:/usr/lib/jvm/default-java/jre/lib/amd64/server" >> /home/ros/.bashrc && \
    echo "export JAVA_HOME=/usr/lib/jvm/default-java" >> /home/ros/.bashrc && \
    echo source /home/ros/devel/setup.bash >> /home/ros/.bashrc && \
    echo source /home/ros/.bashrc >> /home/ros/.bash_profile

# set pre-build variables: only packages in /opt/ros
# set post-build variables:

ENV ROS_ROOT /opt/ros/indigo/share/ros
ENV ROS_PACKAGE_PATH /home/ros/src:/opt/ros/indigo/share:/opt/ros/indigo/stacks
ENV ROS_MASTER_URI http://localhost:11311
ENV ROS_MAVEN_DEPLOYMENT_REPOSITORY /home/ros/devel/share/maven
ENV ROS_MAVEN_PATH /home/ros/devel/share/maven:/opt/ros/indigo/share/maven
EnV ROS_MAVEN_REPOSITORY http://172.17.42.1:8081/nexus/content/groups/public
ENV ROS_DISTRO=indigo
ENV ROS_WORKSPACE /home/ros
ENV ROS_ETC_DIR=/home/ros/etc:/opt/ros/indigo/etc/ros
ENV ROS_IP 127.0.0.1


ENV PYTHON_VERSION python2.7
ENV LD_LIBRARY_PATH /home/ros/local/lib:/home/ros/devel/lib:/home/ros/devel/lib/x64_64-linux-gnu:/opt/ros/indigo/lib:/opt/ros/indigo/lib/x86_64-linux-gnu:/usr/lib/jvm/default-java/jre/lib/amd64:/usr/lib/jvm/default-java/jre/lib/amd64/server:/opt/ros/indigo/lib/python2.7/dist-packages
ENV LIBRARY_PATH /home/ros/local/lib:/home/ros/devel/lib:/home/ros/devel/lib/x64_64-linux-gnu:/opt/ros/indigo/lib:/opt/ros/indigo/lib/x86_64-linux-gnu:/usr/lib/jvm/default-java/jre/lib/amd64:/usr/lib/jvm/default-java/jre/lib/amd64/server:/opt/ros/indigo/lib/python2.7/dist-packages
ENV CPATH /home/ros/local/include:/home/ros/devel/include:/opt/ros/indigo/include
ENV PATH /home/ros/local/bin:/home/ros/devel/bin:/opt/ros/indigo/bin:.:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
ENV LDFLAGS -L/home/ros/local/lib
ENV CMAKE_INCLUDE_PATH=/home/ros/local/include:/home/ros/devel/include:/opt/ros/indigo/include
ENV CMAKE_LIBRARY_PATH=/home/ros/local/lib:/home/ros/devel/lib:/home/ros/devel/lib/x64_64-linux-gnu:/opt/ros/indigo/lib:/opt/ros/indigo/lib/x86_64-linux-gnu

ENV SWI_HOME_DIR /usr/lib/swi-prolog
ENV APR_HOME /usr
ENV ICU_HOME /usr
ENV XERCES_HOME /usr
ENV USER ros

ENV DOCKER_LINKS mongo_db:mongo
# TODO: Mount mesh_data read only. Note: Meshes can be generated in knowrob_vis and saved in mesh_data 
ENV DOCKER_VOLUMES episode_data:ro mesh_data
#ENV DOCKER_VOLUMES knowrob_data:ro mesh_data:ro


# forward ports: KnowRob webserver + rosbridge
EXPOSE 1111 9090

# Volumes for mounting data containers
VOLUME /home/ros/knowrob_data
VOLUME /home/ros/sandbox
