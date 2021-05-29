from PyQt5.QtWidgets import *

from PyQt5.QtCore import *

from PyQt5.QtGui import *

from PyQt5.uic import loadUiType

import os

from os import path

import sys

from urllib.request import urlopen

import urllib

import pafy

import humanize

FORM_CLASS, _ = loadUiType(path.join(path.dirname(__file__), "MainDownloader.ui"))

class MainApp(QMainWindow, FORM_CLASS):
    def __init__(self, parent=None):
        super(MainApp, self).__init__(parent)
        QMainWindow.__init__(self)
        self.setupUi(self)
        self.Handel_UI()
        self.Handel_Botton()

    def Handel_UI(self):
        self.setWindowTitle('PY Downloader')
        self.setFixedSize(800, 368)

    def Handel_Botton(self):
        self.pushButton.clicked.connect(self.Download)
        self.pushButton_2.clicked.connect(self.Handel_Browse)
        self.pushButton_11.clicked.connect(self.Get_Youtube_video)
        self.pushButton_8.clicked.connect(self.Download_Youtube_video)
        self.pushButton_7.clicked.connect(self.Save_Browse)

        self.pushButton_10.clicked.connect(self.Save_Browse)
        self.pushButton_9.clicked.connect(self.playlist_Download)

    def Handel_Browse(self):
        save_palce = QFileDialog.getSaveFileName(self, caption="Save As", directory="C:/Users/Saleh saleh/Downloads",
                                                 filter="All Files (*.*)")
        text = str(save_palce)
        name = (text[2:-1].split(',')[0].replace("'", ''))
        self.lineEdit_2.setText(name)

    def Handel_Progress(self, blocknum, blocksize, totalsize):
        read = blocknum * blocksize
        if totalsize > 0:
            percent = read * 100 / totalsize
            self.progressBar.setValue(percent)
            QApplication.processEvents()

    def Download(self):
        url = self.lineEdit.text()
        save_location = self.lineEdit_2.text()
        try:
            urllib.request.urlretrieve(url, save_location, self.Handel_Progress)
        except Exception:
            QMessageBox.warning(self, "Download Error", "The Download Faild")
            return

        QMessageBox.information(self, "Download Completed", "The Download Finished")
        self.progressBar.setValue(0)
        self.lineEdit.setText('')
        self.lineEdit_2.setText('')

    def Save_Browse(self):
        save = QFileDialog.getExistingDirectory(self, "Select Download Direcyory")
        self.lineEdit_8.setText(save)
        self.lineEdit_9.setText(save)

    def Get_Youtube_video(self):
        video_link = self.lineEdit_3.text()
        v = pafy.new(video_link)
        st = v.videostreams

        for s in st:
            size = humanize.naturalsize(s.get_filesize())
            data = '{} {} {} {}'.format(s.mediatype, s.extension, s.quality, size)
            self.comboBox.addItem(data)

    def Download_Youtube_video(self):
        video_link = self.lineEdit_3.text()
        save_location = self.lineEdit_8.text()
        v = pafy.new(video_link)
        st = v.videostreams
        quality = self.comboBox.currentIndex()
        print(quality)
        Down = st[quality].download(filepath=save_location)
        QMessageBox.information(self, "Download Completed", "The Video Download Finished")

    def playlist_Download(self):
        playlist_url = self.lineEdit_10.text()
        save_location = self.lineEdit_9.text()
        # playlist = pafy.get_playlist(playlist_url)
        playlist2 = pafy.get_playlist2(playlist_url)
        videos = playlist, playlist2['items']
        qs['key'] = g.api_key
        os.chdir(save_location)

        if os.path.exists(str(playlist, playlist2['title'])):
            os.chdir(str(playlist, playlist2['title']))
        else:
            os.mkdir(str(playlist, playlist2['title']))
            os.chdir(str(playlist, playlist2['title']))

        for video in videos:
            p = video['pafy']
            best = p.getbest(preftype='mp4')
            best.dowload(p)
def main():
    app = QApplication(sys.argv)
    window = MainApp()
    window.show()
    app.exec_()


if __name__ == '__main__':
    main()
