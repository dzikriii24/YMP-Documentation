# Frontend (frontend_ymp_admin)

> **Catatan:** Dokumentasi bagian sertifikat untuk diimplementasikan ke admin ymp
---

## 📁 Folder Structure

Struktur folder untuk modul **Sertifikat**:

```
src/
  components/
    admin/
      Sertifikat.tsx
```
---

## 📌 File: `src/components/admin/Sertifikat.tsx`

Deskripsi singkat halaman sertifikat, misalnya:

* Admin Sertifikat
* Management Data Sertifikat
* Management Halaman Sertifikat

### Code

```tsx
import React, { useState, useEffect } from "react";
import { Search, Plus, Edit2, Trash2, X, CheckCircle, AlertCircle } from "lucide-react";
import SertifPageManagement from "./SertifPageManagement";
interface Sertif {
  id: number;
  kode: string;
  nama: string;
  status: string;
  event: string;
  link: string;
}

const API_URL = "http://localhost:5000/api/sertifikat";

// Button Component
interface ButtonProps {
  children: React.ReactNode;
  variant?: "primary" | "outline" | "destructive" | "ghost";
  onClick?: (e: React.MouseEvent<HTMLButtonElement>) => void;
  type?: "button" | "submit" | "reset";
  className?: string;
}
const Button: React.FC<ButtonProps> = ({
  children,
  variant = "primary",
  onClick,
  type = "button",
  className = ""
}) => {
  const baseStyles = "px-4 py-2 rounded-lg font-medium transition-all duration-200 flex items-center gap-2";
  const variants: Record<"primary" | "outline" | "destructive" | "ghost", string> = {
    primary: "bg-blue-600 hover:bg-blue-700 text-white shadow-md hover:shadow-lg",
    outline: "border-2 border-gray-300 hover:border-gray-400 text-gray-700 hover:bg-gray-50",
    destructive: "bg-red-600 hover:bg-red-700 text-white shadow-md hover:shadow-lg",
    ghost: "text-gray-600 hover:bg-gray-100"
  };

  const selectedVariant = variant ?? "primary";

  return (
    <button
      type={type}
      onClick={onClick}
      className={`${baseStyles} ${variants[selectedVariant]} ${className}`}
    >
      {children}
    </button>
  );
};

// Input Component
const Input = ({
  placeholder,
  value,
  onChange,
  className = "",
  icon: Icon
}: any) => {
  return (
    <div className="relative">
      {Icon && (
        <Icon className="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400 w-5 h-5" />
      )}
      <input
        type="text"
        placeholder={placeholder}
        value={value}
        onChange={onChange}
        className={`w-full px-4 py-2.5 ${Icon ? 'pl-10' : ''} border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all ${className}`}
      />
    </div>
  );
};

// Select Component
const Select = ({
  value,
  onChange,
  options,
  placeholder = "Pilih..."
}: any) => {
  return (
    <select
      value={value}
      onChange={onChange}
      className="w-full px-4 py-2.5 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all bg-white"
    >
      <option value="">{placeholder}</option>
      {options.map((opt: any) => (
        <option key={opt.value} value={opt.value}>
          {opt.label}
        </option>
      ))}
    </select>
  );
};

// Modal Component
const Modal = ({ open, onClose, title, children }: any) => {
  if (!open) return null;

  return (
    <div className="fixed inset-0 z-[9999] flex items-center justify-center">
      {/* Backdrop with blur */}
      <div
        className="absolute inset-0 bg-black/50 backdrop-blur-sm"
        onClick={onClose}
      />

      {/* Modal Content */}
      <div className="relative bg-white rounded-2xl shadow-2xl w-full max-w-md mx-4 animate-scale-in">
        <div className="flex items-center justify-between p-6 border-b">
          <h2 className="text-xl font-bold text-gray-900">{title}</h2>
          <button
            onClick={onClose}
            className="text-gray-400 hover:text-gray-600 transition-colors"
          >
            <X className="w-6 h-6" />
          </button>
        </div>
        <div className="p-6">{children}</div>
      </div>
    </div>
  );
};

// Confirmation Modal Component
const ConfirmModal = ({ open, onClose, onConfirm, title, message, type = "warning" }: any) => {
  if (!open) return null;

  return (
    <div className="fixed inset-0 z-[9999] flex items-center justify-center">
      <div
        className="absolute inset-0 bg-black/50 backdrop-blur-sm"
        onClick={onClose}
      />

      <div className="relative bg-white rounded-2xl shadow-2xl w-full max-w-sm mx-4 animate-scale-in">
        <div className="p-6">
          <div className="flex items-center gap-4 mb-4">
            {type === "warning" ? (
              <div className="w-12 h-12 rounded-full bg-red-100 flex items-center justify-center flex-shrink-0">
                <AlertCircle className="w-6 h-6 text-red-600" />
              </div>
            ) : (
              <div className="w-12 h-12 rounded-full bg-blue-100 flex items-center justify-center flex-shrink-0">
                <CheckCircle className="w-6 h-6 text-blue-600" />
              </div>
            )}
            <div>
              <h3 className="text-lg font-bold text-gray-900">{title}</h3>
              <p className="text-sm text-gray-600 mt-1">{message}</p>
            </div>
          </div>

          <div className="flex gap-3 mt-6">
            <Button variant="outline" onClick={onClose} className="flex-1">
              Batal
            </Button>
            <Button
              variant={type === "warning" ? "destructive" : "primary"}
              onClick={onConfirm}
              className="flex-1"
            >
              {type === "warning" ? "Hapus" : "Simpan"}
            </Button>
          </div>
        </div>
      </div>
    </div>
  );
};

// Table Components
const Table = ({ children }: any) => (
  <table className="w-full">{children}</table>
);

const TableHead = ({ children }: any) => (
  <thead className="bg-gray-50 border-b border-gray-200">{children}</thead>
);

const TableBody = ({ children }: any) => (
  <tbody className="divide-y divide-gray-200">{children}</tbody>
);

const TableRow = ({ children }: any) => (
  <tr className="hover:bg-gray-50 transition-colors">{children}</tr>
);

const TableHeaderCell = ({ children }: any) => (
  <th className="px-6 py-4 text-left text-xs font-semibold text-gray-700 uppercase tracking-wider">
    {children}
  </th>
);

const TableCell = ({ children, className = "" }: any) => (
  <td className={`px-6 py-4 text-sm text-gray-900 ${className}`}>{children}</td>
);

// Main Component
const SertifikatPage: React.FC = () => {
  const [data, setData] = useState<Sertif[]>([]);
  const [isModalOpen, setModalOpen] = useState(false);
  const [confirmModal, setConfirmModal] = useState<any>({ open: false });
  const [form, setForm] = useState<Partial<Sertif>>({});
  const [search, setSearch] = useState("");
  const [loading, setLoading] = useState(false);

  const fetchCertificates = async () => {
    try {
      setLoading(true);
      const res = await fetch(API_URL);
      const result = await res.json();
      setData(result);
    } catch (err) {
      console.error("Error fetching certificates:", err);
      alert("Gagal memuat data sertifikat!");
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchCertificates();
  }, []);

  const handleSubmit = () => {
    if (!form.kode || !form.nama || !form.status || !form.event || !form.link) {
      alert("Semua field wajib diisi!");
      return;
    }

    setConfirmModal({
      open: true,
      type: "info",
      title: form.id ? "Konfirmasi Edit" : "Konfirmasi Tambah",
      message: form.id
        ? "Apakah Anda yakin ingin menyimpan perubahan data ini?"
        : "Apakah Anda yakin ingin menambahkan sertifikat ini?",
      onConfirm: async () => {
        try {
          if (form.id) {
            await fetch(`${API_URL}/${form.id}`, {
              method: "PUT",
              headers: { "Content-Type": "application/json" },
              body: JSON.stringify(form),
            });
          } else {
            await fetch(API_URL, {
              method: "POST",
              headers: { "Content-Type": "application/json" },
              body: JSON.stringify(form),
            });
          }

          setForm({});
          setModalOpen(false);
          setConfirmModal({ open: false });
          fetchCertificates();
        } catch (err) {
          console.error("Error saving certificate:", err);
          alert("Gagal menyimpan data!");
        }
      }
    });
  };

  const handleDelete = (id: number, nama: string) => {
    setConfirmModal({
      open: true,
      type: "warning",
      title: "Konfirmasi Hapus",
      message: `Apakah Anda yakin ingin menghapus sertifikat atas nama "${nama}"?`,
      onConfirm: async () => {
        try {
          await fetch(`${API_URL}/${id}`, { method: "DELETE" });
          setConfirmModal({ open: false });
          fetchCertificates();
        } catch (err) {
          console.error("Error deleting:", err);
          alert("Gagal menghapus data!");
        }
      }
    });
  };

  const filtered = data.filter((item) =>
    item.kode.toLowerCase().includes(search.toLowerCase()) ||
    item.nama.toLowerCase().includes(search.toLowerCase())
  );

  const statusOptions = [
    { value: "LULUS", label: "LULUS" },
    { value: "TIDAK LULUS", label: "TIDAK LULUS" }
  ];

  return (
    <div className="min-h-screen bg-gray-50 p-8">

      <div className="max-w-7xl mx-auto">
        {/* Ini bagian “Page Management” */}
        <SertifPageManagement />
      </div>
      <div className="max-w-7xl mx-auto">


        {/* Header */}
        <div className="bg-white rounded-xl shadow-sm p-6 mb-6">
          <div className="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
            <div>
              <h1 className="text-3xl font-bold text-gray-900">Manajemen Sertifikat</h1>
              <p className="text-gray-600 mt-1">Kelola data sertifikat peserta dengan mudah</p>
            </div>
            <Button onClick={() => { setForm({}); setModalOpen(true); }}>
              <Plus className="w-5 h-5" />
              Tambah Sertifikat
            </Button>
          </div>
        </div>

        {/* Search */}
        <div className="bg-white rounded-xl shadow-sm p-6 mb-6">
          <Input
            placeholder="Cari berdasarkan kode atau nama..."
            value={search}
            onChange={(e: any) => setSearch(e.target.value)}
            icon={Search}
          />
        </div>

        {/* Table */}
        <div className="bg-white rounded-xl shadow-sm overflow-hidden">
          {loading ? (
            <div className="flex items-center justify-center py-12">
              <div className="animate-spin rounded-full h-10 w-10 border-b-2 border-blue-600"></div>
            </div>
          ) : (
            <div className="overflow-x-auto">
              <Table>
                <TableHead>
                  <tr>
                    <TableHeaderCell>Kode</TableHeaderCell>
                    <TableHeaderCell>Nama</TableHeaderCell>
                    <TableHeaderCell>Status</TableHeaderCell>
                    <TableHeaderCell>Event</TableHeaderCell>
                    <TableHeaderCell>Link</TableHeaderCell>
                    <TableHeaderCell>Aksi</TableHeaderCell>
                  </tr>
                </TableHead>
                <TableBody>
                  {filtered.length ? (
                    filtered.map((item) => (
                      <TableRow key={item.id}>
                        <TableCell className="font-medium">{item.kode}</TableCell>
                        <TableCell>{item.nama}</TableCell>
                        <TableCell>
                          <span className={`inline-flex px-3 py-1 rounded-full text-xs font-semibold ${item.status === "LULUS"
                            ? "bg-green-100 text-green-800"
                            : "bg-red-100 text-red-800"
                            }`}>
                            {item.status}
                          </span>
                        </TableCell>
                        <TableCell>{item.event}</TableCell>
                        <TableCell>
                          <a
                            href={item.link}
                            target="_blank"
                            rel="noopener noreferrer"
                            className="text-blue-600 hover:text-blue-800 font-medium hover:underline"
                          >
                            Lihat Sertifikat
                          </a>
                        </TableCell>
                        <TableCell>
                          <div className="flex gap-2">
                            <button
                              onClick={() => {
                                setForm(item);
                                setModalOpen(true);
                              }}
                              className="p-2 text-blue-600 hover:bg-blue-50 rounded-lg transition-colors"
                              title="Edit"
                            >
                              <Edit2 className="w-4 h-4" />
                            </button>
                            <button
                              onClick={() => handleDelete(item.id, item.nama)}
                              className="p-2 text-red-600 hover:bg-red-50 rounded-lg transition-colors"
                              title="Hapus"
                            >
                              <Trash2 className="w-4 h-4" />
                            </button>
                          </div>
                        </TableCell>
                      </TableRow>
                    ))
                  ) : (
                    <tr>
                      <td colSpan={6} className="text-center py-12">
                        <div className="flex flex-col items-center gap-2">
                          <Search className="w-12 h-12 text-gray-300" />
                          <p className="text-gray-500 font-medium">Tidak ada data ditemukan</p>
                        </div>
                      </td>
                    </tr>
                  )}
                </TableBody>
              </Table>
            </div>
          )}
        </div>
      </div>

      {/* Form Modal */}
      <Modal
        open={isModalOpen}
        onClose={() => setModalOpen(false)}
        title={form.id ? "Edit Sertifikat" : "Tambah Sertifikat Baru"}
      >
        <div className="space-y-4">
          <div className="grid">
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1.5">
                Kode Sertifikat
              </label>
              <Input
                placeholder="Masukkan kode sertifikat"
                value={form.kode || ""}
                onChange={(e: any) => setForm({ ...form, kode: e.target.value })}
              />
            </div>

            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1.5">
                Nama Lengkap
              </label>
              <Input
                placeholder="Masukkan nama lengkap"
                value={form.nama || ""}
                onChange={(e: any) => setForm({ ...form, nama: e.target.value })}
              />
            </div>
          </div>


          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1.5">
              Status
            </label>
            <Select
              value={form.status || ""}
              onChange={(e: any) => setForm({ ...form, status: e.target.value })}
              options={statusOptions}
              placeholder="Pilih status"
            />
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1.5">
              Event / Acara
            </label>
            <Input
              placeholder="Masukkan nama event"
              value={form.event || ""}
              onChange={(e: any) => setForm({ ...form, event: e.target.value })}
            />
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1.5">
              Link Sertifikat
            </label>
            <Input
              placeholder="Masukkan link Google Drive"
              value={form.link || ""}
              onChange={(e: any) => setForm({ ...form, link: e.target.value })}
            />
          </div>

          <div className="flex gap-3 pt-4">
            <Button variant="outline" onClick={() => setModalOpen(false)} className="flex-1">
              Batal
            </Button>
            <Button onClick={handleSubmit} className="flex-1">
              {form.id ? "Simpan Perubahan" : "Tambah"}
            </Button>
          </div>
        </div>
      </Modal>

      {/* Confirmation Modal */}
      <ConfirmModal
        open={confirmModal.open}
        onClose={() => setConfirmModal({ open: false })}
        onConfirm={confirmModal.onConfirm}
        title={confirmModal.title}
        message={confirmModal.message}
        type={confirmModal.type}
      />

      <style>{`
        @keyframes scale-in {
          from {
            opacity: 0;
            transform: scale(0.95);
          }
          to {
            opacity: 1;
            transform: scale(1);
          }
        }
        
        .animate-scale-in {
          animation: scale-in 0.2s ease-out;
        }
      `}</style>
    </div>
  );
};

export default SertifikatPage;
```

---

## 📌 File: `src/lib/generateCertificate.ts`

Fungsi helper untuk generate sertifikat (canvas/pdf/svg, dll).

### Code

```ts
// paste kode asli file ini di sini
```

---

## 📌 API Endpoint: `src/api/certificate/index.ts`

Routing untuk modul sertifikat.

### Code

```ts
// paste kode asli file ini di sini
```

---

## 📌 API: `src/api/certificate/createCertificate.ts`

Logika backend untuk generasi sertifikat.

### Code

```ts
// paste kode asli file ini di sini
```

---

## 📌 Styles: `src/styles/certificate.css`

Style tambahan untuk tampilan sertifikat.

### Code

```css
// paste kode asli file ini di sini
```

---

## 📝 Notes

* Pastikan semua file sudah di-import dengan benar.
* Jika generate sertifikat menggunakan library tertentu (html2canvas, pdfkit, dll) sebutkan di README.
* Jika membutuhkan environment variables, tambahkan section tambahan.

---

## ✅ Checklist Dokumentasi

* [ ] Foldering jelas
* [ ] Semua file ditulis di README
* [ ] Setiap file ada deskripsi singkat
* [ ] Kode asli sudah ditempel

---

Tinggal kamu isi detailnya + paste kode project kamu sendiri.
