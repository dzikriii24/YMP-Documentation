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
      SertifPageManagement.tsx
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

## 📌 File: `src/admin/SertifPageManagement.tsx`

untuk management page sertifikat, file ini hanya component yang nantinya  dipanggil di Sertifikat.tsx

### Code

```tsx
import React, { FC, useState, useEffect, useMemo } from 'react';
import Table from './Table';
import Modal from 'react-modal';
import { Column } from 'react-table';
import axios from 'axios';
import { useSearch } from '../../context/SearchContext';

interface PageData {
  id: number;
  section: string;
  field_name: string;
  content: string;
}

const SertifPageManagement: FC = () => {
  const [data, setData] = useState<PageData[]>([]);
  const [editingItem, setEditingItem] = useState<PageData | null>(null);
  const [isEditModalOpen, setIsEditModalOpen] = useState(false);
  const [isLoading, setIsLoading] = useState(false);
  const [saveConfirm, setSaveConfirm] = useState(false);
  const { searchQuery } = useSearch();

  const api = axios.create({
    baseURL: import.meta.env.VITE_API_BASE_URL,
    timeout: 5000,
    headers: { 'Content-Type': 'application/json' }
  });

  const fetchData = async () => {
    try {
      const res = await api.get('/api/page-management');
      setData(res.data.sort((a: PageData, b: PageData) => a.id - b.id));
    } catch (err) {
      console.error('Error fetching page data:', err);
      alert('Gagal memuat data halaman');
    }
  };

  useEffect(() => {
    fetchData();
  }, []);

  const handleEditClick = (item: PageData) => {
    setEditingItem(item);
    setIsEditModalOpen(true);
  };

  const handleEditSubmit = async () => {
    if (!editingItem) return;

    setIsLoading(true);
    try {
      const res = await api.put(`/api/page-management/${editingItem.id}`, editingItem);
      if (res.status === 200) {
        await fetchData();
        setIsEditModalOpen(false);
        setSaveConfirm(false);
        setEditingItem(null);

        // Success notification
        alert('Perubahan berhasil disimpan!');
      }
    } catch (err: any) {
      console.error('Update failed:', err);
      alert(`Gagal menyimpan perubahan: ${err.message}`);
    } finally {
      setIsLoading(false);
    }
  };

  const handleSaveConfirm = () => {
    setSaveConfirm(true);
  };

  const getFieldDisplayName = (fieldName: string) => {
    const fieldNames: { [key: string]: string } = {
      // Sertifikat Section
      'header': 'Judul Header',
      'description_1': 'Deskripsi Halaman',
      'input_placeholder': 'Placeholder Input',
      'btn_label_1': 'Label Button 1',
      'button': 'Text Button',

      // Detail Sertifikat Section
      'title_success': 'Judul Success',
      'label_certificate_id': 'Label Nomor Sertifikat',
      'label_name': 'Label Nama Peserta',
      'label_category': 'Label Kategori',
      'label_status': 'Label Status',
      'description_success': 'Deskripsi Success',

        // Field baru untuk detail_sertifikat
      'title_error': 'Judul Error',
      'title_invalid': 'Judul Invalid',
      'title_unknown': 'Judul Unknown Response',
      'message_loading': 'Pesan Loading',
      'message_empty_input': 'Pesan Input Kosong',
      'message_success': 'Pesan Success',
      'message_contact': 'Pesan Kontak',
      'message_invalid': 'Pesan Invalid',
      'message_error': 'Pesan Error',
      'message_unknown': 'Pesan Unknown Response',
      'message_connection_error': 'Pesan Koneksi Error',
      'button_open_drive': 'Text Button Google Drive',
      'preview_title': 'Judul Preview',
      'preview_unavailable': 'Pesan Preview Tidak Tersedia',
      'preview_click_here': 'Text Link Preview'
    };

    return fieldNames[fieldName] || fieldName;
  };

  const getSectionDisplayName = (section: string) => {
    const sectionNames: { [key: string]: string } = {
      'sertifikat': 'Page Management',
      'detail_sertifikat': 'Detail Sertifikat Management'
    };

    return sectionNames[section] || section;
  };

  const columns: Column<PageData>[] = [
    {
      Header: 'ID',
      accessor: 'id',
      width: 70
    },
    {
      Header: 'Field',
      accessor: 'field_name',
      Cell: ({ value }) => (
        <div>
          <div className="font-medium text-gray-900">{getFieldDisplayName(value)}</div>
          <div className="text-xs text-gray-500 font-mono">{value}</div>
        </div>
      )
    },
    {
      Header: 'Konten',
      accessor: 'content',
      Cell: ({ value }) => (
        <div className="max-w-xs">
          <div className="truncate" title={value}>
            {value}
          </div>
        </div>
      )
    },
    {
      Header: 'Aksi',
      width: 100,
      Cell: ({ row }) => (
        <button
          onClick={() => handleEditClick(row.original)}
          className="inline-flex items-center gap-1 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-lg text-sm font-medium hover:bg-blue-100 transition-colors border border-blue-200"
        >
          <svg className="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
          </svg>
          Edit
        </button>
      ),
    },
  ];

  const filteredData = useMemo(() => {
    return data.filter((item) =>
      Object.values(item).some((value) =>
        value.toString().toLowerCase().includes(searchQuery.toLowerCase())
      )
    );
  }, [searchQuery, data]);

  const sertifikatData = useMemo(() =>
    filteredData.filter(item => item.section === 'sertifikat'),
    [filteredData]
  );

  const detailSertifikatData = useMemo(() =>
    filteredData.filter(item => item.section === 'detail_sertifikat'),
    [filteredData]
  );

  return (
    <div className="min-h-screen bg-gray-50/30 py-8">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        {/* Header */}
        <div className="mb-8">
          <h1 className="text-3xl font-bold text-gray-900">Page Content Management</h1>
          <p className="text-gray-600 mt-2">
            Kelola konten dinamis untuk halaman sertifikat dan detail sertifikat
          </p>
        </div>

        {/* Page Management Section */}
        <div className="bg-white rounded-xl shadow-sm border border-gray-100 mb-8 overflow-hidden">
          <div className="px-6 py-4 border-b border-gray-100 bg-gradient-to-r from-blue-50 to-indigo-50">
            <div className="flex items-center gap-3">
              <div className="w-2 h-8 bg-blue-500 rounded-full"></div>
              <div>
                <h2 className="text-xl font-semibold text-gray-900">Page Management</h2>
                <p className="text-gray-600 text-sm">
                  Kelola konten untuk halaman utama sertifikat
                </p>
              </div>
            </div>
          </div>

          <div className="p-6">
            {sertifikatData.length > 0 ? (
              <Table
                columns={columns}
                data={sertifikatData}
                className="compact-table"
              />
            ) : (
              <div className="text-center py-12">
                <svg className="w-16 h-16 mx-auto text-gray-300 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={1} d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                </svg>
                <p className="text-gray-500 text-lg font-medium">Tidak ada data</p>
                <p className="text-gray-400 text-sm mt-1">Data akan muncul di sini</p>
              </div>
            )}
          </div>
        </div>

        {/* Detail Sertifikat Management Section */}
        <div className="bg-white rounded-xl shadow-sm border border-gray-100 overflow-hidden">
          <div className="px-6 py-4 border-b border-gray-100 bg-gradient-to-r from-green-50 to-emerald-50">
            <div className="flex items-center gap-3">
              <div className="w-2 h-8 bg-green-500 rounded-full"></div>
              <div>
                <h2 className="text-xl font-semibold text-gray-900">Detail Sertifikat Management</h2>
                <p className="text-gray-600 text-sm">
                  Kelola konten untuk halaman detail sertifikat
                </p>
              </div>
            </div>
          </div>

          <div className="p-6">
            {detailSertifikatData.length > 0 ? (
              <Table
                columns={columns}
                data={detailSertifikatData}
                className="compact-table"
              />
            ) : (
              <div className="text-center py-12">
                <svg className="w-16 h-16 mx-auto text-gray-300 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={1} d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                </svg>
                <p className="text-gray-500 text-lg font-medium">Tidak ada data</p>
                <p className="text-gray-400 text-sm mt-1">Data akan muncul di sini</p>
              </div>
            )}
          </div>
        </div>

        {/* Edit Modal */}
        <Modal
          isOpen={isEditModalOpen}
          onRequestClose={() => {
            setIsEditModalOpen(false);
            setSaveConfirm(false);
            setEditingItem(null);
          }}
          className="modal p-4 rounded-2xl shadow-2xl w-full max-w-md mx-auto my-8 bg-white"
          overlayClassName="modal-overlay fixed inset-0 bg-black bg-opacity-40 flex justify-center items-center p-4 backdrop-blur-sm"
          ariaHideApp={false}
        >
          {!saveConfirm ? (
            <div className="p-6">
              <div className="flex items-center gap-3 mb-6">
                <div className="w-8 h-8 bg-blue-100 rounded-lg flex items-center justify-center">
                  <svg className="w-4 h-4 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
                  </svg>
                </div>
                <h2 className="text-xl font-semibold text-gray-900">Edit Konten</h2>
              </div>

              <form onSubmit={(e) => { e.preventDefault(); handleSaveConfirm(); }} className="space-y-5">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">Field</label>
                  <input
                    type="text"
                    value={editingItem ? getFieldDisplayName(editingItem.field_name) : ''}
                    disabled
                    className="w-full bg-gray-50 border border-gray-200 rounded-xl px-4 py-3 text-gray-600"
                  />
                </div>

                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">Konten</label>
                  <textarea
                    value={editingItem?.content || ''}
                    onChange={(e) =>
                      setEditingItem((prev) => ({ ...prev!, content: e.target.value }))
                    }
                    required
                    rows={4}
                    className="w-full border border-gray-300 rounded-xl px-4 py-3 focus:ring-2 focus:ring-blue-500 focus:border-blue-500 resize-vertical transition-colors"
                    placeholder="Masukkan konten..."
                  />
                </div>

                <div className="flex justify-end gap-3 pt-4">
                  <button
                    type="button"
                    onClick={() => {
                      setIsEditModalOpen(false);
                      setEditingItem(null);
                    }}
                    className="px-6 py-2.5 border border-gray-300 rounded-xl text-sm font-medium text-gray-700 hover:bg-gray-50 transition-colors"
                  >
                    Batal
                  </button>
                  <button
                    type="submit"
                    className="px-6 py-2.5 bg-blue-600 text-white rounded-xl text-sm font-medium hover:bg-blue-700 transition-colors shadow-sm"
                  >
                    Simpan Perubahan
                  </button>
                </div>
              </form>
            </div>
          ) : (
            <div className="p-6 text-center">
              <div className="w-16 h-16 bg-yellow-100 rounded-full flex items-center justify-center mx-auto mb-4">
                <svg className="w-8 h-8 text-yellow-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
                </svg>
              </div>

              <h3 className="text-lg font-semibold text-gray-900 mb-2">Konfirmasi Penyimpanan</h3>
              <p className="text-gray-600 mb-6">
                Apakah Anda yakin ingin menyimpan perubahan pada konten ini?
              </p>

              <div className="flex justify-center gap-3">
                <button
                  onClick={() => setSaveConfirm(false)}
                  className="px-6 py-2.5 border border-gray-300 rounded-xl text-sm font-medium text-gray-700 hover:bg-gray-50 transition-colors"
                >
                  Kembali Edit
                </button>
                <button
                  onClick={handleEditSubmit}
                  disabled={isLoading}
                  className="px-6 py-2.5 bg-blue-600 text-white rounded-xl text-sm font-medium hover:bg-blue-700 disabled:opacity-50 transition-colors flex items-center gap-2"
                >
                  {isLoading ? (
                    <>
                      <div className="animate-spin rounded-full h-4 w-4 border-b-2 border-white"></div>
                      Menyimpan...
                    </>
                  ) : (
                    'Ya, Simpan Perubahan'
                  )}
                </button>
              </div>
            </div>
          )}
        </Modal>
      </div>
    </div>
  );
};

export default SertifPageManagement;
```

---


# API yang diambil di page ini : localhost:5000/api/page-management


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
