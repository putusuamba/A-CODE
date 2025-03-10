#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

int pilihan1 = 0;
int pilihan2 = 0;
char barang[5][20] = {"Buku Tulis", "Pensil", "Penghapus", "Penggaris", "Bujur Sangkar"};
int harga[5] = {5000, 2000, 1000, 1000, 500};
char namaBarang[5][20];
int hargaBarang[5];
int jumlah[5];
float diskon[5];
int sortedIndex[5] = {0,1,2,3,4};
int totalHarga = 0;
int totalDiskon = 0;
int totalBayar;
int bayar;
int lastIndex = 0;

void printGaris(int length)
{
    for(int i=0; i<length; i++){
        printf("=");
    }
}

void printPilihan()
{
    printf("\nSelamat Datang di Toko Skensa\nSilahkan pilih barang yang Anda Inginkan\n");
    printGaris(37);
    printf("\n| %-3s | %-13s | %-11s |\n", "No.","Nama Barang","Harga");
    printGaris(37);

    printf("\n");
    for(int i=0; i<5; i++) {
     printf("| %-3d | %-13s | Rp.%-8d |\n", i+1, barang[i], harga[i]);
    }
    printGaris(37);
    printf("\n99. Struk Pembayaran");
    printf("\n55. Reset Pilihan");
    printf("\n00. Keluar\n");
    printGaris(37);
    printf("\n");
}

float cekDiskon(int harga, int jumlah)
{
    if (jumlah > 5) {
        return jumlah * harga * 0.15;
    }else if (jumlah > 3){
        return jumlah * harga * 0.1;
    }
    return 0;
}

char *getTimeNow()
{
    time_t rawtime;
    struct tm *timeinfo;
    time(&rawtime);
    timeinfo = localtime(&rawtime);
    char *datetime = asctime(timeinfo);
    return datetime;
}

char *generateID()
{
    time_t t = time(NULL);
    struct tm tm = *localtime(&t);
    char *id = malloc(sizeof(char) * 20);
    sprintf(id, "%d%d%d%d%d%d", tm.tm_year + 1900, tm.tm_mon + 1, tm.tm_mday, tm.tm_hour, tm.tm_min, tm.tm_sec);
    return id;
}

void cetakStruk(int totalHarga, int totalDiskon, int totalBayar, int bayar, int kembalian)
{
    FILE *file;
    char *id = generateID();
    char *filename[strlen(id) + 5];
    sprintf(filename, "%s.txt", id);

    file = fopen(filename, "w");
    fprintf(file,"=========================================================================\n");
    fprintf(file,"                               Toko SKENSA                               \n");
    fprintf(file,"                   Jl. HOS Cokroaminoto No 84 Denpasar                   \n");
    fprintf(file,"                                  Bali                                   \n");
    fprintf(file,"                             Telp: 0816285791                            \n");
    fprintf(file,"ID Struk: %s\n", id);
    fprintf(file,"=========================================================================\n");
    fprintf(file,"|Nama Barang        |Harga      |Total Harga|Diskon  |\n");
    for(int i=0; i<lastIndex; i++){
        float total = jumlah[i] * hargaBarang[i];
        fprintf(file,"|%2dx  %s    |Rp.%d    |Rp. %6.0f|Rp. %5.0f|\n", jumlah[i], namaBarang[i], hargaBarang[i], total, diskon[i]);
    }
    fprintf(file,"=========================================================================\n");
    fprintf(file, "Total Harga = Rp %d\n", totalHarga);
    fprintf(file, "Total Diskon = Rp %d\n", totalDiskon);
    fprintf(file, "Tagihan = Rp %d\n", totalBayar);
    fprintf(file, "Pembayaran = Rp %d\n", bayar);
    fprintf(file, "Kembalian = Rp %d\n\n\n", kembalian);

    char *timeNow = getTimeNow();
    fprintf(file, "Waktu : %s", timeNow);
    fclose(file);

    printf("Struk sudah di cetak pada file %s", filename);
}

int main()
{
    while (1){
        printPilihan();
        printf("Input pilihan yang Anda inginkan : ");
        scanf("%d", &pilihan1);
            while (pilihan1 == 55){
                for(int i=0; i<5; i++){
                    strcpy(namaBarang[i], "");
                    jumlah[i] = 0;
                    diskon[i] = 0;
                    hargaBarang[i] = 0;
                }
            lastIndex = 0;
            printPilihan();
            printf("Input pilihan yang Anda inginkan : ");
            scanf("%d", &pilihan1);
        }
        if (pilihan1 == 99){
            if(lastIndex > 0) {
                printGaris(97);
                printf("\n| %-3s | %-15s | %-15s | %-15s | %-15s | %-15s |\n", "No.", "Jumlah", "Nama Barang", "Harga", "Total", "Diskon");
                for(int i=0; i<lastIndex-1; i++) {
                    for(int j=0; j<lastIndex-i-1; j++) {
                        if(jumlah[sortedIndex[j]] < jumlah[sortedIndex[j+1]]){
                            int temp = sortedIndex[j];
                            sortedIndex[j] = sortedIndex[j+1];
                            sortedIndex[j+1] = temp;
                        }
                    }
                }

                for(int i=0; i<lastIndex; i++) {
                    int index = sortedIndex[i];
                    totalHarga += jumlah[index] * hargaBarang[index];
                    totalDiskon += diskon[index];
                    float total = jumlah[index] * hargaBarang[index];
                    printf("| %-3d | %-15d | %-15s | Rp.%-12d | Rp.%-12.0f | Rp.%-12.0f |\n", i+1, jumlah[index], namaBarang[index], hargaBarang[index], total, diskon[index]);
                }
                printGaris(97);
                printf("\nTotal Harga  = Rp. %d,-\n", totalHarga);
                printf("Total Diskon = Rp. %d,-\n", totalDiskon);
                totalBayar = totalHarga - totalDiskon;
                printf("Total Bayar  = Rp. %d,-\n", totalBayar);
                printGaris(97);
                printf("\nMasukkan uang bayar = ");
                scanf("%d", &bayar);
                while(bayar < totalBayar){
                    printf("Uang yang dimasukkan kurang!\n");
                    printf("Masukkan uang bayar = ");
                    scanf("%d", &bayar);
                }
                printf("Kembalian = %d\n", bayar - totalBayar);
                cetakStruk(totalHarga, totalDiskon, totalBayar, bayar, bayar-totalBayar);
                return 0;
            } else {
                printf("Anda belum memilih barang!\n\n");
            }
        }
        if (pilihan1 == 0 || pilihan1 == 00){
            return;
        }
        if(pilihan1 > 0 && pilihan1 <= 5){
            printf("Masukkan Jumlah Barang : ");
            scanf("%d", &pilihan2);
            printf("\n");

            int has = 0;
            for(int i=0; i<lastIndex; i++) {
                if(strcasecmp(barang[pilihan1 - 1], namaBarang[i]) == 0) {
                    jumlah[i] += pilihan2;
                    diskon[i] = cekDiskon(hargaBarang[i], jumlah[i]);
                    has = 1;
                }
                break;
            }

            if(!has) {
                strcpy(namaBarang[lastIndex], barang[pilihan1 - 1]);
                jumlah[lastIndex] = pilihan2;
                hargaBarang[lastIndex] = harga[pilihan1 - 1];
                diskon[lastIndex] = cekDiskon(harga[pilihan1-1], pilihan2);
                lastIndex += 1;
            }

            printf("Keranjang anda : \n");
            for(int i=0; i<lastIndex; i++) {
                printf("%s (%dx)\n", namaBarang[i], jumlah[i]);
            }
            printGaris(40);
        } else {
            printf("Barang tidak ditemukan! \n\n");
        }
    }
    return 0;
}
