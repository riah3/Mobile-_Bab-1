import 'dart:async';
enum Role { Admin, Pelanggan }

class Produk {
  final String namaProduk;
  final double harga;
  final bool tersedia;

  Produk({required this.namaProduk, required this.harga, required this.tersedia});
}

class Pengguna {
  String nama;
  int usia;
  late List<Produk>? daftarProduk;
  Role? peran;

  Pengguna({required this.nama, required this.usia, this.peran}) {
    daftarProduk = []; 
  }
}

class AdminPengguna extends Pengguna {
  AdminPengguna({required String nama, required int usia}) : super(nama: nama, usia: usia, peran: Role.Admin);

  void tambahProduk(Produk produk) {
    if (!produk.tersedia) {
      throw Exception('Produk tidak tersedia dalam stok!');
    }
    daftarProduk!.add(produk);
    print('${produk.namaProduk} telah ditambahkan.');
  }

  void hapusProduk(String namaProduk) {
    daftarProduk!.removeWhere((produk) => produk.namaProduk == namaProduk);
    print('$namaProduk telah dihapus dari daftar.');
  }
}

class PelangganPengguna extends Pengguna {
  PelangganPengguna({required String nama, required int usia}) : super(nama: nama, usia: usia, peran: Role.Pelanggan);

  void lihatDaftarProduk() {
    if (daftarProduk!.isEmpty) {
      print('Tidak ada produk yang tersedia.');
    } else {
      print('Daftar Produk:');
      for (var produk in daftarProduk!) {
        print('${produk.namaProduk} - Rp${produk.harga} - ${produk.tersedia ? "Tersedia" : "Tidak tersedia"}');
      }
    }
  }
}

Future<Produk> ambilDetailProduk(String namaProduk) async {
  print('Mengambil detail produk dari server...');
  await Future.delayed(Duration(seconds: 2));
  return Produk(namaProduk: namaProduk, harga: 50000.0, tersedia: true);
}

void main() async {
  Set<Produk> setProduk = {};

  Map<String, Produk> petaProduk = {
    'Laptop': Produk(namaProduk: 'Laptop', harga: 7000000, tersedia: true),
    'Mouse': Produk(namaProduk: 'Mouse', harga: 50000, tersedia: true),
    'Keyboard': Produk(namaProduk: 'Keyboard', harga: 100000, tersedia: false),
  };

  var admin = AdminPengguna(nama: 'Andi', usia: 35);

  try {
    admin.tambahProduk(petaProduk['Laptop']!);
    admin.tambahProduk(petaProduk['Keyboard']!);
  } on Exception catch (e) {
    print('Kesalahan: ${e.toString()}');
  }

  setProduk.add(petaProduk['Laptop']!);
  setProduk.add(petaProduk['Mouse']!);
  setProduk.add(petaProduk['Laptop']!); 

  print('\nProduk dalam set:');
  for (var produk in setProduk) {
    print('${produk.namaProduk} - Rp${produk.harga}');
  }

  var pelanggan = PelangganPengguna(nama: 'Budi', usia: 25);
  pelanggan.daftarProduk = admin.daftarProduk;

  pelanggan.lihatDaftarProduk();

  var produkBaru = await ambilDetailProduk('Headphone');
  print('\nDetail Produk dari Server:');
  print('${produkBaru.namaProduk} - Rp${produkBaru.harga} - ${produkBaru.tersedia ? "Tersedia" : "Tidak tersedia"}');
}
