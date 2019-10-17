.. _pdf:

合并 pdf
========================================

.. code-block:: python

    """
    合并多个 pdf 到一个，使用方式：放到当前文件夹下运行
    """

    import os.path

    from pyPdf import PdfFileReader, PdfFileWriter # pip install pyPdf


    def get_pdf_files(dst_dir):
        paths = []
        for root, dirs, files in os.walk(dst_dir):
            for filespath in files:
                if filespath.endswith('.pdf'):  # pdf file
                    abspath = os.path.join(root, filespath)
                    paths.append(abspath)
        return paths


    ##########################合并同一个文件夹下所有PDF文件########################
    def merge_pdf(dst_dir, outfile, sort=True):
        output = PdfFileWriter()
        curpage = 0
        pdf_paths = sorted(get_pdf_files(dst_dir)) if sort else get_pdf_files(dst_dir)
        for each in pdf_paths:
            print(each)
            reader = PdfFileReader(file(each, "rb"))
            # 如果pdf文件已经加密，必须首先解密才能使用pyPdf
            if reader.isEncrypted == True:
                reader.decrypt("map")
            # 获得源pdf文件中页面总数
            page_count = reader.getNumPages()
            curpage += page_count
            print(page_count)

            for iPage in range(0, page_count):
                output.addPage(reader.getPage(iPage))

        print("All Pages Number:" + str(curpage))
        outputStream = file(dst_dir + outfile, "wb")
        output.write(outputStream)
        outputStream.close()


    def main():
        merged = "all.pdf"
        merge_pdf("./", merged)


    if __name__ == '__main__':
        main()
