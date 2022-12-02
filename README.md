# deepstream


https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/1.14.5
https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/gst-plugins-base-1.14.5
https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/gst-rtsp-server-1.14.5
https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/gst-plugins-good-1.14.5
https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/gst-devtools-1.14.5



install Miniconda3 Linux-aarch64 64-bit:

https://conda.io/en/latest/miniconda.html


install meson:
(base) nvidia@nvidia-desktop:~$ pip3 install meson


install naja:
(base) nvidia@nvidia-desktop:~$sudo apt-get install ninja-build


build:
mkdir build
meson --prefix ~/gstlibs build
ninja -C build -j 8
meson install -C build


debug:
install dot : sudo apt-get install graphviz

export GST_DEBUG_DUMP_DOT_DIR=/home/nvidia/Documents/streams/gstreamer/dot
gst-launch-1.0 videotestsrc ! ximagesink


dot -Tpng 0.00.03.908536374-gst-launch.PLAYING_READY.dot  > 0.00.03.908536374-gst-launch.PLAYING_READY.png

cp ~/Documents/streams/deepstream-5.1/sources/libs/libleafproto/Debug-jetson/libleafproto.so ./libs
cp libnvdsgst_dsexample.so /home/nvidia/hica/gst-plugin/

export GST_PLUGIN_PATH=/home/nvidia/hica/gst-plugin/:$GST_PLUGIN_PATH
export LD_LIBRARY_PATH=/home/nvidia/hica/libs/:$LD_LIBRARY_PATH

gst-launch-1.0 playbin uri=file:///home/nvidia/Documents/streams/yolov5/deepstream5.1.mp4
gst-launch-1.0 audiotestsrc ! wavescope ! videoconvert ! ximagesink


ls -al /usr/lib/aarch64-linux-gnu/libstdc++.so.6



{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name":"deepstream-app",
            "type":"lldb",
            "request": "launch",
            "program": "/home/nvidia/Documents/streams/yolov5/deepstream-app",
            "args":["-c","/home/nvidia/Documents/streams/yolov5/deepstream_app_config_yoloV5.txt"],
            "cwd":"/home/nvidia/Documents/streams/yolov5/",
            "env":{
               "GST_PLUGIN_PATH":"/home/nvidia/hica/gst-plugin/:$GST_PLUGIN_PATH",
                "LD_LIBRARY_PATH":"/home/nvidia/hica/libs/:$LD_LIBRARY_PATH"
            }
        }
    ]
}





export NVDS_ENABLE_LATENCY_MEASUREMENT=1
export NVDS_ENABLE_COMPONENT_LATENCY_MEASUREMENT=1

export GST_PLUGIN_PATH=/home/nvidia/gstlibs/lib/aarch64-linux-gnu/:$GST_PLUGIN_PATH



 编译自己的gstreamer：
 
 1编译如下两个模块：
 
https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/1.14.5
https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/gst-plugins-base-1.14.5

 --prefix ~/gstlibs
 
2 export PKG_CONFIG_PATH=~/gstlibs/lib/aarch64-linux-gnu/pkgconfig/:$PKG_CONFIG_PATH

3确认是否切到自己的编译路径下：pkg-config --libs gstreamer-1.0

4到自己的工程下直接make




gst-launch-1.0 filesrc location = sample_1080p_h264.mp4 ! decodebin ! m.sink_0 \
filesrc location = sample_1080p_h264.mp4 ! decodebin ! m.sink_1 \
filesrc location = sample_1080p_h264.mp4 ! decodebin ! m.sink_2 \
filesrc location = sample_1080p_h264.mp4 ! decodebin ! m.sink_3 \
nvstreammux name=m width=1920 height=1080 batch-size=4 batched-push-timeout=40000 ! \
queue ! nvinfer config-file-path=<config> batch-size=4 ! \
queue ! nvtracker ll-lib-file=<lib-file> ! \
vstreamdemux name=d \
d.src_1 ! queue ! nvvideoconvert ! nvdsosd ! nvvideoconvert ! nvv4l2h264enc ! h264parse ! qtmux ! filesink location=out.mp4 \
d.src_2 ! queue ! nvvideoconvert ! nvdsosd ! nveglglessink


gst-launch-1.0 filesrc location = sample_1080p_h264.mp4 ! decodebin ! m.sink_0 \
    filesrc location = sample_1080p_h264.mp4 ! decodebin ! m.sink_1 \
    filesrc location = sample_1080p_h264.mp4 ! decodebin ! m.sink_2 \
    filesrc location = sample_1080p_h264.mp4 ! decodebin ! m.sink_3 \
    nvstreammux name=m width=1920 height=1080 batch-size=4 batched-push-timeout=40000 ! \
    queue ! nvinfer config-file-path=<config> batch-size=4 ! \
    queue ! nvtracker ll-lib-file=<lib-file> ! \
    nvstreamdemux name=d \
    d.src_0 ! queue ! nvvideoconvert ! nvdsosd ! nveglglessink \
    d.src_1 ! queue ! nvvideoconvert ! nvdsosd ! nveglglessink \
    d.src_2 ! queue ! nvvideoconvert ! nvdsosd ! nveglglessink \
    d.src_3 ! queue ! nvvideoconvert ! nvdsosd ! nveglglessink

.. note::
   Queue element should be inserted after every ``nvstreamdemux src`` pad.
   
   
   JPEG decode

Using nvv4l2decoder on Jetson:

$ gst-launch-1.0 filesrc location= ./streams/sample_720p.jpg ! jpegparse ! nvv4l2decoder ! nvegltransform ! nveglglessink
Using nvv4l2decoder on dGPU:

$ gst-launch-1.0 filesrc location= ./streams/sample_720p.jpg ! jpegparse ! nvv4l2decoder ! nveglglessink
Using nvjpegdec on Jetson:

$ gst-launch-1.0 filesrc location= ./streams/sample_720p.jpg ! nvjpegdec ! nvegltransform !  nveglglessink
Using nvjpegdec on dGPU:

$ gst-launch-1.0 filesrc location= ./streams/sample_720p.jpg ! nvjpegdec !   nveglglessink

参考：https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_FAQ.html?highlight=gst%20launch
