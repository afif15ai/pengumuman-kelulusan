<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Pengumuman Kelulusan</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      background-color: #f4f4f4;
      padding: 20px;
    }
    img.logo {
      width: 30%;
      max-width: 200px;
      height: auto;
      margin-bottom: 10px;
    }
    h1 {
      color: #2c3e50;
      font-size: 2em;
      margin-bottom: 20px;
    }
    form {
      margin-top: 20px;
    }
    input, button {
      padding: 12px;
      margin: 8px;
      width: 80%;
      max-width: 300px;
      font-size: 1em;
    }
    #result {
      margin-top: 30px;
      font-size: 18px;
    }
    @media (max-width: 600px) {
      img.logo {
        width: 50%;
      }
      h1 {
        font-size: 1.5em;
      }
    }
  </style>
</head>
<body>
  <img src="logo-smks.png" alt="SMK Sriwijaya Karangpucung" class="logo">
  <h1>Pengumuman Kelulusan</h1>

  <input type="file" id="excelFile" accept=".xlsx, .xls">

  <form id="cekForm">
    <input type="text" id="nisn" placeholder="Masukkan NISN" required><br>
    <input type="date" id="tglLahir" placeholder="Tanggal Lahir" required><br>
    <button type="submit">Cek Kelulusan</button>
  </form>

  <div id="result"></div>

  <script>
    let siswaData = [];

    document.getElementById('excelFile').addEventListener('change', function(e) {
      const reader = new FileReader();
      reader.onload = function(e) {
        const data = new Uint8Array(e.target.result);
        const workbook = XLSX.read(data, { type: 'array' });
        const sheet = workbook.Sheets[workbook.SheetNames[0]];
        siswaData = XLSX.utils.sheet_to_json(sheet);
        alert("File berhasil dimuat!");
      };
      reader.readAsArrayBuffer(e.target.files[0]);
    });

    document.getElementById('cekForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const nisnInput = document.getElementById('nisn').value.trim();
      const tglLahirInput = document.getElementById('tglLahir').value.trim();
      const resultDiv = document.getElementById('result');

      const siswa = siswaData.find(row => {
        return String(row.nisn).trim() === nisnInput && String(row.tgl_lahir).trim() === tglLahirInput;
      });

      if (!siswa) {
        resultDiv.innerHTML = '<p style="color:red">Data tidak ditemukan. Pastikan NISN dan Tanggal Lahir sesuai.</p>';
        return;
      }

      const status = siswa.status.toLowerCase() === 'lulus' ?
        `<p><strong>${siswa.nama}</strong></p><p style="color:green">Selamat! Anda dinyatakan LULUS.</p>` :
        `<p><strong>${siswa.nama}</strong></p><p style="color:red">Mohon maaf, Anda BELUM LULUS.</p>`;

      resultDiv.innerHTML = status;
    });
  </script>
</body>
</html>
