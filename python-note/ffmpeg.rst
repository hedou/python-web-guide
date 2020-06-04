.. _ffmpeg:


使用Python 编写的 ffmepg 处理视频的脚本
========================================

拼接视频片段
___________________________________

.. code-block:: python

    #!/usr/bin/env python
    # -*- coding:utf-8 -*-

    """
    使用 ffmpeg 截取视频片段并且重新拼接

    使用方式:
    提供文件格式如下：比如 input.txt

    ./input.mp4
    00:01:00 00:02:00
    00:04:00 00:08:00
    """

    import os
    import sys

    CONCAT_FILE = '_concat.txt'


    def read_input(filepath):
        with open(filepath) as f:
            lines = f.readlines()
            input_mp4 = lines[0].strip()
            suffix = input_mp4.split('.')[-1]
            for idx, start_end_time in enumerate(lines[1:]):
                pair = start_end_time.split()
                start, end = pair[0], pair[1]
                part_name = 'part_' + str(idx) + '.' + suffix
                cmd = "ffmpeg -i {} -ss {} -to {} -c copy {}".format(
                    input_mp4, start, end, part_name
                )
                print(cmd)
                os.system(cmd)
                yield part_name


    def write_part_to_file(part_list):
        dir_path = os.path.dirname(os.path.realpath(__file__))
        filepath_list = []
        for part_name in part_list:
            print(part_name)
            path = os.path.join(dir_path, part_name)
            filepath_list.append(path)

        with open(CONCAT_FILE, 'w') as f:
            for path in filepath_list:
                f.write("file '{}'\n".format(path))
        return filepath_list


    def concat_video():
        cmd = "ffmpeg -f concat -safe 0 -i {} -c copy output.mp4".format(CONCAT_FILE)
        os.system(cmd)


    def remove(filepath_list):
        """移除中间文件"""
        for path in filepath_list + [CONCAT_FILE]:
            if os.path.exists(path):
                os.remove(path)


    def main():
        try:
            inputfile = sys.argv[1]
        except KeyError:
            print('must need input.txt')
        partnames = list(read_input(inputfile))
        filepath_list = write_part_to_file(partnames)
        concat_video()
        remove(filepath_list)


    if __name__ == '__main__':
        main()



递归统计一个文件夹下的所有视频时长
___________________________________________

.. code-block:: python

    # -*- coding: utf-8 -*-

    """
    递归统计一个目录下所有视频文件的总时长

    pip install moviepy

    如果安装报错，尝试升级

    pip install --upgrade setuptools

    使用方法：
    python compute_duration.py --path ~/Movies/ --type .mp4

    参考：https://blog.csdn.net/qq_22210253/article/details/86684658
    """

    import os
    import datetime
    import argparse
    from moviepy.editor import VideoFileClip


    def main():
        parser = argparse.ArgumentParser(
            description='Compute Total Time of a Series of Videos')
        parser.add_argument("--path", metavar="PATH", default=".",
                            help="the root path of the videos(default: .)")
        parser.add_argument("--type", metavar="TYPE", default=".mkv",
                            help="the type of the videos(default: .mkv)")
        args = parser.parse_args()
        filelist = []
        for a, b, c in os.walk(args.path):
            for name in c:
                fname = os.path.join(a, name)
                if fname.endswith(args.type):
                    filelist.append(fname)
        ftime = 0.0
        for file in sorted(filelist):
            clip = VideoFileClip(file)
            print("{}: {}秒".format(file, clip.duration))
            ftime += clip.duration
        print("%d seconds: " % ftime, str(datetime.timedelta(seconds=ftime)))


    if __name__ == "__main__":
        main()
