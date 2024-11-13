' Histogram Equalization
For i = 0 To Picture1.ScaleWidth - 1
    For j = 0 To Picture1.ScaleHeight - 1
        ' Mengambil warna piksel
        w = Picture1.Point(i, j)
        r = w And RGB(255, 0, 0)
        g = Int((w And RGB(0, 255, 0)) / 256)
        b = Int(Int((w And RGB(0, 0, 255)) / 256) / 256)
        
        ' Menghitung nilai grayscale rata-rata
        xg = Int((r + g + b) / 3)
        
        ' Melakukan transformasi untuk equalization
        yg = Int(256 * xg / 128 / 128)
        h2(yg) = h2(yg) + 1
        
        ' Menyetel piksel baru pada Picture2
        Picture2.PSet (i, j), RGB(yg, yg, yg)
    Next j
Next i

' Menampilkan Histogram
For i = 0 To 255
    ' Mengisi data untuk MSChart1 dengan histogram asli (h)
    MSChart1.Row = i + 1
    MSChart1.Data = h(i)
    MSChart1.RowLabel = Trim(Str(i))
    
    ' Mengisi data untuk MSChart2 dengan histogram yang sudah di-equalize (h2)
    MSChart2.Row = i + 1
    MSChart2.Data = h2(i)
    MSChart2.RowLabel = Trim(Str(i))
Next i
