#include "admin.h"
#include "ui_admin.h"
#include "datapasien.h"
#include "inputdata.h"  // Mengimpor InputData
#include <QMessageBox>

Admin::Admin(QWidget *parent)
    : QWidget(parent),
    ui(new Ui::Admin)
{
    ui->setupUi(this);
    this->setFixedSize(500, 700);

    // Koneksi tombol-tombol
    connect(ui->tambah1, &QPushButton::clicked, this, &Admin::tambahPasien);
    connect(ui->hapus1, &QPushButton::clicked, this, &Admin::hapusPasien);
    connect(ui->tambah2, &QPushButton::clicked, this, &Admin::editPasien);
    connect(ui->next, &QPushButton::clicked, this, &Admin::pergiKeInputData);  // Koneksi tombol next

    // Tampilkan data saat pertama kali buka
    tampilkanData();
}

Admin::~Admin()
{
    delete ui;
}

void Admin::tambahPasien()
{
    QString id = ui->id1->text();
    QString nama = ui->nmpasien1->text();
    int umur = ui->umr1->text().toInt();
    QString nomorHp = ui->nmrhp1->text();
    QString alamat = ui->almt1->text();
    QString keluhan = ui->klhn1->text();

    if (id.isEmpty() || nama.isEmpty() || umur == 0 || nomorHp.isEmpty() || alamat.isEmpty() || keluhan.isEmpty()) {
        QMessageBox::warning(this, "Error", "Semua kolom harus diisi!");
        return;
    }

    DataPasien::Pasien newPasien = {id, nama, nomorHp, keluhan, alamat, umur};
    DataPasien::instance().addPasien(newPasien);

    QMessageBox::information(this, "Sukses", "Pasien berhasil ditambahkan!");

    tampilkanData();
}

void Admin::hapusPasien()
{
    QString id = ui->id1->text();
    if (id.isEmpty()) {
        QMessageBox::warning(this, "Error", "Masukkan ID untuk menghapus pasien!");
        return;
    }

    DataPasien::instance().removePasien(id);

    QMessageBox::information(this, "Sukses", "Pasien berhasil dihapus (jika ID ditemukan)!");

    tampilkanData();
}

void Admin::editPasien()
{
    QString id = ui->id2->text();
    QString nama = ui->nmpasien2->text();
    int umur = ui->umr2->text().toInt();
    QString nomorHp = ui->nmrhp2->text();
    QString alamat = ui->almt2->text();
    QString keluhan = ui->klhn2->text();

    if (id.isEmpty() || nama.isEmpty() || umur == 0 || nomorHp.isEmpty() || alamat.isEmpty() || keluhan.isEmpty()) {
        QMessageBox::warning(this, "Error", "Semua kolom harus diisi untuk edit!");
        return;
    }

    DataPasien::Pasien updatedPasien = {id, nama, nomorHp, keluhan, alamat, umur};
    bool updated = DataPasien::instance().updatePasien(updatedPasien);

    if (updated) {
        QMessageBox::information(this, "Sukses", "Data pasien berhasil diupdate!");
    } else {
        QMessageBox::warning(this, "Gagal", "ID pasien tidak ditemukan!");
    }

    tampilkanData();
}

void Admin::pergiKeInputData()
{
    inputdata *inputWindow = new inputdata();  // Membuka jendela InputData
    inputWindow->show();
    this->close();  // Menutup jendela Admin
}

void Admin::tampilkanData()
{
    QList<DataPasien::Pasien> pasienList = DataPasien::instance().getPasienList();
    QString output;

    for (const auto &p : pasienList) {
        output += QString("ID: %1\nNama: %2\nUmur: %3\nNo HP: %4\nAlamat: %5\nKeluhan: %6\n-------------------\n")
        .arg(p.id)
            .arg(p.nama)
            .arg(p.umur)
            .arg(p.nomorHp)
            .arg(p.alamat)
            .arg(p.keluhan);
    }

    ui->kolomkolom->setPlainText(output);
}
