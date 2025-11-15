# Frontend (front_end_ymp_admin)

> **Catatan:** Dokumentasi bagian sertifikat untuk diimplementasikan ke admin ymp
---

## 📁 Folder Structure

Struktur folder untuk modul **Frontend Sertifikat**:

```
src/
  components/
    admin/
      CardAdmin.tsx
      Sertifikat.tsx
      SertifPageManagement.tsx
      Sidebar.tsx
```
---

## 📌 CardAdmin.tsx: `src/components/admin/CardAdmin.tsx`

Menambahkan Route Untuk Sertifikat (update CardAdmin.tsx).

### Code

```tsx
import { useState } from "react";
import { Routes, Route, Navigate } from "react-router-dom";
import Sidebar from "./Sidebar";
import Header from "./Header";
import DashboardContent from "./DashboardContent";
import TetangKami from "./TetangKami";
import Konsultasi from "./Konsultasi";
import KontakKami from "./KontakKami";
import SocialConnect from "./SocialConnect";
import ProjectBimble from "./ProjectBimble";
import ProgramContent from "./ProgramContent";
import SettingsContent from "./SettingsContent";
import { SearchProvider } from "../../context/SearchContext";
import ProtectedRoute from "../ProtectedRoute";
import "../styles/styleAdmin.css";
import Logs from "./Logs";
import Sertifikat from "./Sertifikat";

const Dashboard: React.FC = () => {
  const [isOpen, setIsOpen] = useState<boolean>(true);

  return (
    <SearchProvider>
      <div className="dashboard">
        <ProtectedRoute>
          <>
            <Sidebar isOpen={isOpen} toggleSidebar={() => setIsOpen(!isOpen)} />
            <main className="main-content">
              <Header />
              <Routes>
                <Route path="/" element={<DashboardContent />} />
                <Route path="/tetangkami" element={<TetangKami />} />
                <Route path="/konsultasi" element={<Konsultasi />} />
                <Route path="/projectbimble" element={<ProjectBimble />} />
                <Route path="/programLainnya" element={<ProgramContent />} />
                <Route path="/kontakKami" element={<KontakKami />} />
                <Route path="/socialconnect" element={<SocialConnect />} />
                <Route path="/settings" element={<SettingsContent />} />
                <Route path="/logs" element={<Logs />} />
                <Route path="*" element={<Navigate to="/" replace />} />
                <Route path="/sertifikat" element={<Sertifikat />} />
              </Routes>
            </main>
          </>
        </ProtectedRoute>
      </div>
    </SearchProvider>
  );
};

export default Dashboard;
```

---

## 📌 Sertifikat.tsx: `src/components/admin/Sertifikat.tsx`

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
> **API yang diambil di page ini:** [localhost:5000/api/sertifikat](http://localhost:5000/api/sertifikat)



## 📌 SertifPageManagement.tsx: `src/admin/SertifPageManagement.tsx`

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

> **API yang diambil di page ini:** [http://localhost:5000/api/page-management](http://localhost:5000/api/page-management)



## 📌 Sidebar.tsx: `src/components/admin/Sidebar.tsx`

Add Sesi Sertifikat di admin YMP (Update Sidebar).

### Code

```tsx
import { FC, useState, useRef, useEffect } from "react";
import { NavLink, useLocation } from "react-router-dom";
import {
  FiMenu,
  FiHome,
  FiUsers,
  FiMessageSquare,
  FiShoppingBag,
  FiLogOut,
  FiActivity,
  FiChevronRight,
} from "react-icons/fi";
import { IoIosArrowDropdown } from "react-icons/io";
import { PiGameControllerFill } from "react-icons/pi";

import { TbMilitaryRank } from "react-icons/tb";

import {
  MdOutlineWidgets,
  MdPhonelinkSetup,
  MdOutlineConnectWithoutContact,
} from "react-icons/md";
import { LuSettings2 } from "react-icons/lu";
import { GrCertificate } from "react-icons/gr";
import Swal from "sweetalert2";
import Yukmari from "../../assets/YukMari.svg";

interface SidebarProps {
  isOpen: boolean;
  toggleSidebar: () => void;
}

const Sidebar: FC<SidebarProps> = ({ isOpen, toggleSidebar }) => {
  const location = useLocation();
  const [isPageOpen, setIsPageOpen] = useState(false);
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);
  const dropdownRef = useRef<HTMLLIElement>(null);

  // Track window resize for responsive behavior
  useEffect(() => {
    function handleResize() {
      setWindowWidth(window.innerWidth);
    }

    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  // Close dropdown when clicking outside
  useEffect(() => {
    function handleClickOutside(event: MouseEvent) {
      if (
        dropdownRef.current &&
        !dropdownRef.current.contains(event.target as Node)
      ) {
        setIsPageOpen(false);
      }
    }

    document.addEventListener("mousedown", handleClickOutside);
    return () => {
      document.removeEventListener("mousedown", handleClickOutside);
    };
  }, []);

  // Check if current route is in the dropdown menu
  useEffect(() => {
    if (
      location.pathname === "/dashboard/navigasi" ||
      location.pathname === "/dashboard/footer"
    ) {
      setIsPageOpen(true);
    }
  }, [location.pathname]);

  const handleLogout = async () => {
    const result = await Swal.fire({
      title: "Apakah Anda yakin ingin logout?",
      text: "Anda harus login kembali untuk mengakses dashboard!",
      icon: "warning",
      showCancelButton: true,
      confirmButtonColor: "#d33",
      cancelButtonColor: "#3085d6",
      confirmButtonText: "Ya, Logout!",
      cancelButtonText: "Batal",
    });

    if (result.isConfirmed) {
      try {
        const response = await fetch(
          `${import.meta.env.VITE_API_BASE_URL}/api/logout`,
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            credentials: "include",
          }
        );

        if (response.ok) {
          localStorage.clear();
          window.location.href = "/?logoutSuccess=true";
        } else {
          Swal.fire("Error", "Logout gagal, silakan coba lagi.", "error");
        }
      } catch (error) {
        Swal.fire("Error", "Terjadi kesalahan saat logout.", "error");
      }
    }
  };

  // Determine if we should use mobile view dropdown behavior
  const isMobileView = windowWidth <= 768;

  return (
    <aside className={`sidebar ${!isOpen ? "collapsed" : ""}`}>
      <div className="sidebar-header">
        {isOpen && <img src={Yukmari} alt="Yukmari" />}
        <button className="toggle-btn" onClick={toggleSidebar}>
          <FiMenu />
        </button>
      </div>
      <nav className="sidebar-nav">
        <ul>
          <li className={location.pathname === "/dashboard" ? "active" : ""}>
            <NavLink to="/dashboard">
              <FiHome />
              {isOpen && <span>Beranda</span>}
            </NavLink>
          </li>

          {/* <li ref={dropdownRef} className={`dropdown ${isPageOpen ? 'active' : ''}`}>
            <button className="dropdown-toggle" onClick={() => setIsPageOpen(!isPageOpen)}>
              <IoIosArrowDropdown />
              {isOpen && <span>Page</span>}
              {isOpen && <FiChevronRight className={`ml-auto transition-transform ${isPageOpen ? 'rotate-90' : ''}`} />}
            </button>
            <ul className="dropdown-menu">
              <li className={location.pathname === '/dashboard/navigasi' ? 'active' : ''}>
                <NavLink to="/dashboard/navigasi">
                  {isMobileView || isOpen ? 'Navigasi (RU)' : 'Nav'}
                </NavLink>
              </li>
              <li className={location.pathname === '/dashboard/footer' ? 'active' : ''}>
                <NavLink to="/dashboard/footer">
                  {isMobileView || isOpen ? 'Footer (RU)' : 'Footer'}
                </NavLink>
              </li>
            </ul>
          </li> */}

          <li
            className={
              location.pathname === "/dashboard/tetangkami" ? "active" : ""
            }
          >
            <NavLink to="/dashboard/tetangkami">
              <FiUsers />
              {isOpen && <span>Tentang Kami</span>}
            </NavLink>
          </li>

          <li
            className={
              location.pathname === "/dashboard/konsultasi" ? "active" : ""
            }
          >
            <NavLink to="/dashboard/konsultasi">
              <FiMessageSquare />
              {isOpen && <span>Konsultasi</span>}
            </NavLink>
          </li>
          <li
            className={
              location.pathname === "/dashboard/sertifikat" ? "active" : ""
            }
          >
            <NavLink to="/dashboard/sertifikat">
              <GrCertificate />
              {isOpen && <span>Sertifikat</span>}
            </NavLink>
          </li>
          <li
            className={
              location.pathname === "/dashboard/projectbimble" ? "active" : ""
            }
          >
            <NavLink to="/dashboard/projectbimble">
              <FiShoppingBag />
              {isOpen && <span>Project & Bimble</span>}
            </NavLink>
          </li>
          <li
            className={
              location.pathname === "/dashboard/programLainnya" ? "active" : ""
            }
          >
            <NavLink to="/dashboard/programLainnya">
              <MdOutlineWidgets />
              {isOpen && <span>Program Lainnya</span>}
            </NavLink>
          </li>
          <li
            className={
              location.pathname === "/dashboard/kontakKami" ? "active" : ""
            }
          >
            <NavLink to="/dashboard/kontakKami">
              <MdPhonelinkSetup />
              {isOpen && <span>Kontak Kami</span>}
            </NavLink>
          </li>
          <li
            className={
              location.pathname === "/dashboard/socialconnect" ? "active" : ""
            }
          >
            <NavLink to="/dashboard/socialconnect">
              <MdOutlineConnectWithoutContact />
              {isOpen && <span>Social Connect</span>}
            </NavLink>
          </li>
          <li
            className={
              location.pathname === "/dashboard/settings" ? "active" : ""
            }
          >
            <NavLink to="/dashboard/settings">
              <LuSettings2 />
              {isOpen && <span>Pengaturan</span>}
            </NavLink>
          </li>
          <li
            className={location.pathname === "/dashboard/logs" ? "active" : ""}
          >
            <NavLink to="/dashboard/logs">
              <FiActivity />
              {isOpen && <span>Logs</span>}
            </NavLink>
          </li>
          <ul className="bottom-menu">
            <li>
              <NavLink
                to="/"
                onClick={(e) => {
                  e.preventDefault();
                  handleLogout();
                }}
              >
                <FiLogOut />
                {isOpen && <span>Logout</span>}
              </NavLink>
            </li>
          </ul>
        </ul>
      </nav>
    </aside>
  );
};

export default Sidebar;
```

---

# Backend (back_end_ymp_admin)

> **Catatan:** Dokumentasi bagian sertifikat untuk connect ke database (DBeaver) dan dikirim ke Frontend melalui API ke admin ymp
---

## 📁 Folder Structure

Struktur folder untuk modul **Backend Sertifikat**:

```
src/
  config/
      database.ts
  controllers/
      detailSertifController.ts
      sertifPageManagementController.ts
  models/
      detailSertifManagement.ts
      Sertifikat.ts
      sertifPageManagement.ts
  routes/
      detailSertifRoutes.ts
      pageConfigRoutes.ts
      sertifikatRoutes.ts
      sertifikatYMP.ts
      sertifPageManagementRoutes.ts
app.ts
```
---
## 📌 database.ts: `src/config/database.ts`

(update database.ts).

### Code

```ts
import { Pool } from "pg";
import dotenv from "dotenv";

dotenv.config();

const pool = new Pool({
  user: process.env.DB_USER,
  host: process.env.DB_HOST,
  database: process.env.DB_NAME,
  password: process.env.DB_PASSWORD,
  port: parseInt(process.env.DB_PORT || "5432"),
  ssl: {
    rejectUnauthorized: false,
  },
});

pool
  .connect()
  .then(() => {
    console.log("Connected to the database");
  })
  .catch((err) => {
    console.error("Failed to connect to database:", err.message);
  });

export default pool;
```

---

## 📌 detailSertifController.ts: `src/controllers/detailSertifController.ts`


### Code

```ts
import { Request, Response } from 'express';
import * as Model from '../models/detailSertifManagement';

// GET all
export const listDetails = async (req: Request, res: Response): Promise<void> => {
  try {
    const rows = await Model.getAllDetails();
    res.json(rows);
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Database error' });
  }
};

// GET by field name
export const getDetail = async (req: Request, res: Response): Promise<void> => {
  try {
    const field = req.params.field;
    const row = await Model.getDetailByField(field);
    if (!row) {
      res.status(404).json({ message: 'Not found' });
      return;
    }
    res.json(row);
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Database error' });
  }
};

// CREATE
export const createDetail = async (req: Request, res: Response): Promise<void> => {
  const { field_name, content } = req.body;
  if (!field_name || content == null) {
    res.status(400).json({ message: 'Missing fields' });
    return;
  }

  try {
    await Model.insertDetail({ field_name, content });
    res.status(201).json({ message: 'Created' });
  } catch (err: any) {
    console.error(err);
    res.status(500).json({ message: err.message });
  }
};

// UPDATE
export const updateDetail = async (req: Request, res: Response): Promise<void> => {
  const id = Number(req.params.id);
  const { content } = req.body;
  try {
    await Model.updateDetail(id, { content });
    res.json({ message: 'Updated' });
  } catch (err: any) {
    console.error(err);
    res.status(500).json({ message: err.message });
  }
};

// DELETE
export const deleteDetail = async (req: Request, res: Response): Promise<void> => {
  const id = Number(req.params.id);
  try {
    await Model.deleteDetail(id);
    res.json({ message: 'Deleted' });
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Database error' });
  }
};
```

---

## 📌 sertifPageManagementController.ts: `src/controllers/sertifPageManagementController.ts`


### Code

```ts
import { Request, Response } from 'express';
import * as PageModel from '../models/sertifPageManagement';

export const listPages = async (req: Request, res: Response): Promise<void> => {
  try {
    const pages = await PageModel.getAllPages();
    res.json(pages);
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Database error' });
  }
};

export const listPagesBySection = async (req: Request, res: Response): Promise<void> => {
  try {
    const section = req.params.section;
    const pages = await PageModel.getPagesBySection(section);
    res.json(pages);
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Database error' });
  }
};

export const getPage = async (req: Request, res: Response): Promise<void> => {
  try {
    const id = Number(req.params.id);
    const page = await PageModel.getPageById(id);
    if (!page) {
      res.status(404).json({ message: 'Not found' });
      return;
    }
    res.json(page);
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Database error' });
  }
};

export const createPage = async (req: Request, res: Response): Promise<void> => {
  try {
    const { section, field_name, content } = req.body;
    if (!section || !field_name || !content) {
      res.status(400).json({ message: 'Missing fields' });
      return;
    }
    await PageModel.insertPage({ section, field_name, content });
    res.status(201).json({ message: 'Created' });
  } catch (err: any) {
    console.error(err);
    res.status(500).json({ message: err.message });
  }
};

export const updatePage = async (req: Request, res: Response): Promise<void> => {
  try {
    const id = Number(req.params.id);
    const { field_name, content } = req.body;
    await PageModel.updatePage(id, { field_name, content });
    res.json({ message: 'Updated' });
  } catch (err: any) {
    console.error(err);
    res.status(500).json({ message: err.message });
  }
};

export const removePage = async (req: Request, res: Response): Promise<void> => {
  try {
    const id = Number(req.params.id);
    await PageModel.deletePage(id);
    res.json({ message: 'Deleted' });
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Database error' });
  }
};
```

---

## 📌 detailSertifManagement.ts: `src/models/detailSertifManagement.ts`


### Code

```ts
import pool from '../config/database';

export interface DetailRow {
  id?: number;
  field_name: string;
  content: string;
  created_at?: string;
  updated_at?: string;
}

export const getAllDetails = async (): Promise<DetailRow[]> => {
  const res = await pool.query('SELECT * FROM detail_sertif_management ORDER BY id');
  return res.rows;
};

export const getDetailByField = async (field_name: string): Promise<DetailRow | null> => {
  const res = await pool.query('SELECT * FROM detail_sertif_management WHERE field_name = $1', [field_name]);
  return res.rows[0] || null;
};

export const insertDetail = async (row: DetailRow): Promise<void> => {
  await pool.query('INSERT INTO detail_sertif_management (field_name, content) VALUES ($1, $2)', [row.field_name, row.content]);
};

export const updateDetail = async (id: number, row: Partial<DetailRow>): Promise<void> => {
  await pool.query('UPDATE detail_sertif_management SET content = $1, updated_at = CURRENT_TIMESTAMP WHERE id = $2', [row.content, id]);
};

export const deleteDetail = async (id: number): Promise<void> => {
  await pool.query('DELETE FROM detail_sertif_management WHERE id = $1', [id]);
};
```

---

## 📌 Sertifikat.ts: `src/models/Sertifikat.ts`

### Code

```ts
import pool from '../config/database';

export interface Certificate {
    id?: number;
    kode: string;
    nama: string;
    status: string;
    event: string;
    link: string;
}
export const getCertificates = async (): Promise<Certificate[]> => {
    try {
        const result = await pool.query('SELECT * FROM sertifikat ORDER BY created_at DESC');
        return result.rows;
    } catch (err) {
        console.error('Error fetching sertifikat:', err);
        throw new Error('Database error');
    }
};
export const getCertificateByKode = async (kode: string): Promise<Certificate | null> => {
    try {
        const result = await pool.query('SELECT * FROM sertifikat WHERE kode = $1', [kode]);
        return result.rows[0] || null;
    } catch (err) {
        console.error('Error fetching certificate by kode:', err);
        throw new Error('Database error');
    }
};
export const insertCertificate = async (data: Certificate): Promise<void> => {
    const { kode, nama, status, event, link } = data;
    if (!kode || !nama || !status || !event || !link)
        throw new Error('Semua field wajib diisi');
    try {
        await pool.query(
            'INSERT INTO sertifikat (kode, nama, status, event, link) VALUES ($1, $2, $3, $4, $5)',
            [kode, nama, status, event, link]
        );
    } catch (err: any) {
        console.error('Error inserting certificate:', err);
        throw new Error(`Database error: ${err.message}`);
    }
};
export const updateCertificate = async (id: number, data: Certificate): Promise<void> => {
    const { kode, nama, status, event, link } = data;
    try {
        await pool.query(
            'UPDATE sertifikat SET kode=$1, nama=$2, status=$3, event=$4, link=$5 WHERE id=$6',
            [kode, nama, status, event, link, id]
        );
    } catch (err) {
        console.error('Error updating certificate:', err);
        throw new Error('Database error');
    }
};
export const deleteCertificate = async (id: number): Promise<void> => {
    try {
        await pool.query('DELETE FROM sertifikat WHERE id = $1', [id]);
    } catch (err) {
        console.error('Error deleting certificate:', err);
        throw new Error('Database error');
    }
};
```

---

## 📌 sertifPageManagement.ts: `src/models/sertifPageManagement.ts`


### Code

```ts
import pool from '../config/database';

export interface PageRow {
  id?: number;
  section: string;
  field_name: string;
  content: string;
  created_at?: string;
  updated_at?: string;
}

export const getAllPages = async (): Promise<PageRow[]> => {
  const res = await pool.query('SELECT * FROM sertif_page_management ORDER BY id');
  return res.rows;
};

export const getPagesBySection = async (section: string): Promise<PageRow[]> => {
  const res = await pool.query('SELECT * FROM sertif_page_management WHERE section = $1 ORDER BY id', [section]);
  return res.rows;
};

export const getPageById = async (id: number): Promise<PageRow | null> => {
  const res = await pool.query('SELECT * FROM sertif_page_management WHERE id = $1', [id]);
  return res.rows[0] || null;
};

export const insertPage = async (row: PageRow): Promise<void> => {
  await pool.query(
    'INSERT INTO sertif_page_management (section, field_name, content) VALUES ($1, $2, $3)',
    [row.section, row.field_name, row.content]
  );
};

export const updatePage = async (id: number, row: Partial<PageRow>): Promise<void> => {
  await pool.query(
    'UPDATE sertif_page_management SET field_name = $1, content = $2, updated_at = CURRENT_TIMESTAMP WHERE id = $3',
    [row.field_name, row.content, id]
  );
};

export const deletePage = async (id: number): Promise<void> => {
  await pool.query('DELETE FROM sertif_page_management WHERE id = $1', [id]);
};

```

---



## 📌 detailSertifRoutes.ts: `src/routes/detailSertifRoutes.ts`


### Code

```ts
import { Router } from 'express';
import * as Ctrl from '../controllers/detailSertifController';

const detailSertifManagements = Router();

detailSertifManagements.get('/', Ctrl.listDetails);
detailSertifManagements.get('/field/:field', Ctrl.getDetail);
detailSertifManagements.post('/', Ctrl.createDetail);
detailSertifManagements.put('/:id', Ctrl.updateDetail);
detailSertifManagements.delete('/:id', Ctrl.deleteDetail);

export default detailSertifManagements;
```

---

## 📌 sertifikatRoutes.ts: `src/routes/sertifikatRoutes.ts`

### Code

```ts
// src/routes/certificateRoutes.ts
import express from 'express';
import {
    getAllCertificates,
    getByKode,
    createCertificate,
    updateCert,
    deleteCert,
} from '../routes/sertifikatYmp';

const SertifikatRouter = express.Router();

SertifikatRouter.get('/', getAllCertificates);
SertifikatRouter.get('/:kode', getByKode);
SertifikatRouter.post('/', createCertificate);
SertifikatRouter.put('/:id', updateCert);
SertifikatRouter.delete('/:id', deleteCert);

export default SertifikatRouter;
```

---

## 📌 database.ts: `src/routes/pageConfigRoutes.ts`

### Code

```ts
import { Router } from "express";
import {
  getPageConfig,
  updatePageConfig,
} from "../controllers/pageConfigController";

const PageConfig = Router();

PageConfig.get("/", getPageConfig);
PageConfig.put("/", updatePageConfig);

export default PageConfig;
```

---


## 📌 sertifikatYMP.ts: `src/routes/sertifikatYMP.ts`

### Code

```ts
// src/controllers/certificateController.ts
import { Request, Response } from 'express';
import {
  getCertificates,
  getCertificateByKode,
  insertCertificate,
  updateCertificate,
  deleteCertificate,
} from '../models/Sertifikat';

export const getAllCertificates = async (req: Request, res: Response): Promise<void> => {
  try {
    const data = await getCertificates();
    res.json(data);
  } catch (err: any) {
    res.status(500).json({ message: err.message });
  }
};

export const getByKode = async (req: Request, res: Response): Promise<void> => {
  try {
    const kode = req.params.kode;
    const data = await getCertificateByKode(kode);
    if (!data) {
      res.status(404).json({ message: 'Sertifikat tidak ditemukan' });
      return;
    }
    res.json(data);
  } catch (err: any) {
    res.status(500).json({ message: err.message });
  }
};

export const createCertificate = async (req: Request, res: Response): Promise<void> => {
  try {
    await insertCertificate(req.body);
    res.status(201).json({ message: 'Sertifikat berhasil ditambahkan' });
  } catch (err: any) {
    res.status(400).json({ message: err.message });
  }
};

export const updateCert = async (req: Request, res: Response): Promise<void> => {
  try {
    const id = Number(req.params.id);
    await updateCertificate(id, req.body);
    res.json({ message: 'Sertifikat berhasil diperbarui' });
  } catch (err: any) {
    res.status(400).json({ message: err.message });
  }
};

export const deleteCert = async (req: Request, res: Response): Promise<void> => {
  try {
    const id = Number(req.params.id);
    await deleteCertificate(id);
    res.json({ message: 'Sertifikat berhasil dihapus' });
  } catch (err: any) {
    res.status(400).json({ message: err.message });
  }
};

```

---

## 📌 sertifPageManagementRoutes.ts: `src/routes/sertifPageManagementRoutes.ts`


### Code

```ts
import { Router } from 'express';
import * as Ctrl from '../controllers/sertifPageManagementController';

const sertifPageManagements = Router();

sertifPageManagements.get('/', Ctrl.listPages);
sertifPageManagements.get('/section/:section', Ctrl.listPagesBySection);
sertifPageManagements.get('/:id', Ctrl.getPage);
sertifPageManagements.post('/', Ctrl.createPage);
sertifPageManagements.put('/:id', Ctrl.updatePage);
sertifPageManagements.delete('/:id', Ctrl.removePage);

export default sertifPageManagements;
```

---


## 📌 app.ts: `app.ts`


### Code

```ts
import express from "express";
import dotenv from "dotenv";
import bodyParser from "body-parser";
import cors from "cors";
import adminRoutes from "./routes/adminRoutes";
import aboutUsRoutes from "./routes/aboutUsRoutes";
import authRoutes from "./routes/authRoutes";
import logoutRouter from "./routes/logout";
import forgetPasswordRoute from "./routes/forgetPasswordRoute";
import konsultasiRoutes from "./routes/konsultasiroute";
import projectBimbleRoutes from "./routes/projectBimbleRoutes";
import { Request, Response, NextFunction } from "express";
import footerRoutes from "./routes/footerRoutes";
import kontakKamiRouter from "./routes/KontakKamiRoutes";
import programContentRouter from "./routes/ProgramContent";
import projectTestimoniRoutes from "./routes/ProjectTestimoniRoutes";
import KerjasamaDevRoute from "./routes/KerjasamaDevRoute";
import logsRoutes from "./routes/logsRoute";
import testimonialRoutes from "./routes/testimonialRoute";
import projectTestiClientRoute from "./routes/ProjectTestiClientRoute";
import developmentAppLogoRoute from "./routes/DevelopmentApplicationLogoRoute";
import securityMitraLogoRoute from "./routes/securityMitraLogoRoute";
import dokumentasiRoute from "./routes/dokumentasiRoute";
import keunggulanRoute from "./routes/keunggulanRoute";
import paket_1 from "./routes/paket1";
import paket_2 from "./routes/paket2";
import paket_3 from "./routes/paket3";
import profileRoutes from "./routes/profileRoutes";
import pageConfigRoutes from "./routes/pageConfigRoutes";
import sertifikatRoutes from "./routes/sertifikatRoutes";
import sertifPageManagements from "./routes/sertifPageManagementRoutes";

dotenv.config();

const app = express();
app.set("trust proxy", true);

// Configure CORS first
app.use(
  cors({
    origin: [
      "http://localhost:3000",
      "https://www.google.com",
      "http://192.168.100.36:3000",
      "https://ccc9-114-10-45-131.ngrok-free.app",
    ],
    credentials: true,
    methods: ["GET", "POST", "PUT", "DELETE", "OPTIONS"],
    allowedHeaders: [
      "Content-Type",
      "Authorization",
      "x-timezone",
      "Accept",
      "Cross-Origin-Resource-Policy",
    ],
    exposedHeaders: ["Cross-Origin-Resource-Policy"],
  })
);

// Set headers for CORS
app.use((req, res, next) => {
  res.header("Cross-Origin-Resource-Policy", "cross-origin");
  next();
});

// ⚠️ FIX: Hapus duplikasi body parser, gunakan salah satu saja
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true, limit: '10mb' }));

// ⚠️ FIX: Atau gunakan bodyParser (pilih salah satu, jangan keduanya)
// app.use(bodyParser.json({ limit: '10mb' }));
// app.use(bodyParser.urlencoded({ extended: true, limit: '10mb' }));

// ⚠️ FIX: Debug middleware untuk log semua request
app.use((req: Request, res: Response, next: NextFunction) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  console.log('Query params:', req.query);
  console.log('Body:', req.body);
  next();
});

// ⚠️ FIX: Routes - Pastikan path base sudah benar
app.use("/api/page-config", pageConfigRoutes);
app.use("/api/sertifikat", sertifikatRoutes);
app.use("/api/page-management", sertifPageManagements);

// ⚠️ FIX: Tambahkan route test khusus untuk debugging
app.get("/api/ctf-page-management/test", (req: Request, res: Response) => {
  console.log("Test route accessed");
  res.json({ 
    message: "CTF Page Management API is working!",
    timestamp: new Date().toISOString()
  });
});

// Other API routes
app.use("/api", logsRoutes);
app.use("/api", profileRoutes);
app.use(
  "/api",
  adminRoutes,
  aboutUsRoutes,
  authRoutes,
  logoutRouter,
  forgetPasswordRoute,
  konsultasiRoutes,
  projectBimbleRoutes,
  footerRoutes,
  kontakKamiRouter,
  programContentRouter,
  projectTestimoniRoutes,
  KerjasamaDevRoute,
  testimonialRoutes,
  projectTestiClientRoute,
  developmentAppLogoRoute,
  securityMitraLogoRoute,
  dokumentasiRoute,
  keunggulanRoute,
  paket_1,
  paket_2,
  paket_3
);

// Basic route
app.get("/about-us", (req, res) => {
  res.json([{ id: 1, contentType: "text", content: "About Us content" }]);
});

// Global error handling middleware
app.use((err: any, req: Request, res: Response, next: NextFunction) => {
  console.error("Error details:", err);
  res.status(500).json({
    error: "Internal Server Error",
    message: err.message || "Something went wrong",
  });
});

// 404 Handler untuk route yang tidak ditemukan
app.use("*", (req: Request, res: Response) => {
  console.log(`404 - Route not found: ${req.method} ${req.originalUrl}`);
  res.status(404).json({
    error: "Route not found",
    method: req.method,
    url: req.originalUrl,
    availableRoutes: [
      "GET /api/ctf-page-management",
      "GET /api/ctf-page-management/test",
      "PUT /api/ctf-page-management/:id",
      "GET /api/ctf-page-management/section/:section"
    ]
  });
});

// Health check endpoint
app.get("/api/health", (req: Request, res: Response) => {
  res.json({
    status: "OK",
    message: "CTF Ranking API is running",
    timestamp: new Date().toISOString(),
  });
});

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
  console.log(`Health check: http://localhost:${PORT}/api/health`);
  console.log(`CTF Page Management Test: http://localhost:${PORT}/api/ctf-page-management/test`);
  console.log(`CTF Page Management Main: http://localhost:${PORT}/api/ctf-page-management`);
});

export default app;
```

---





# Frontend (ymp_web_dev)

> **Catatan:** Dokumentasi bagian frontend website yuk-mari-project
---

## 📁 Folder Structure

Struktur folder untuk modul **Frontend Sertifikat**:

```
assets/
  js/
    virtual-classroom/
      sertifikat_cek.js
  .env
  cek-sertifikat-s-proses.php
  cek-sertifikat-seminar.php
  cek-sertifikat.php
  database.php
  sertif-page-management.php
  Sertifikat.php
```
---

## 📌 sertifikat_cek.js: `assets/js/virtual-classroom/sertifikat_cek.js`


### Code

```js
$(document).ready(function() {
    let dynamicContent = {};
    
    // Load dynamic content dari PHP
    function loadDynamicContent() {
        $.ajax({
            url: 'sertif-page-management.php',
            type: 'GET',
            dataType: 'json',
            success: function(response) {
                dynamicContent = response;
            },
            error: function() {
                console.warn('Gagal memuat konten dinamis, menggunakan fallback');
                // Fallback content
                dynamicContent = {
                    title_success: 'Sertifikat Ditemukan!',
                    title_invalid: 'Sertifikat Tidak Ditemukan',
                    title_error: '⚠️ Terjadi Kesalahan',
                    title_unknown: 'Respons Tidak Dikenali',
                    label_certificate_id: 'Nomor Sertifikat:',
                    label_name: 'Nama Peserta:',
                    label_category: 'Kategori:',
                    label_status: 'Status:',
                    message_loading: '🔍 Memeriksa sertifikat...',
                    message_empty_input: 'Masukkan ID sertifikat terlebih dahulu.',
                    message_success: 'Anda sudah mengikuti kegiatan.',
                    message_contact: 'Jika ingin meminta kembali sertifikat, silakan hubungi kami.',
                    message_invalid: 'Silakan ikuti kegiatan terlebih dahulu.',
                    message_error: 'Terjadi kesalahan saat memverifikasi sertifikat.',
                    message_unknown: 'Respons tidak dikenali dari server.',
                    message_connection_error: 'Terjadi kesalahan koneksi',
                    button_open_drive: 'Buka di Google Drive',
                    preview_title: 'Preview Sertifikat:',
                    preview_unavailable: 'Tidak dapat menampilkan preview.',
                    preview_click_here: 'Klik di sini untuk membuka file'
                };
            }
        });
    }

    // Panggil saat halaman load
    loadDynamicContent();

    $('#form-cek-sertifikat-seminar').on('submit', function(e) {
        e.preventDefault();
        const certificateID = $('#certificateID').val().trim();

        if (!certificateID) {
            $('#result').html(`
                <div class="alert alert-warning">
                    ${dynamicContent.message_empty_input || 'Masukkan ID sertifikat terlebih dahulu.'}
                </div>
            `);
            return;
        }

        $('#result').html(`
            <div class="alert alert-info">
                ${dynamicContent.message_loading || '🔍 Memeriksa sertifikat...'}
            </div>
        `);

        $.ajax({
            url: 'cek-sertifikat-s-proses.php',
            type: 'POST',
            dataType: 'json',
            data: { certificateID },
            success: function(response) {
                console.log("Response dari server:", response);
                
                if (response.status === 'valid') {
                    const driveLink = response.drive_link || response.link || '';
                    const embedUrl = getDriveEmbedLink(driveLink);
                    let previewHTML = '';
                    
                    if (embedUrl) {
                        previewHTML = `
                            <div class="">
                                <p class="mt-3 mb-0">
                                    <a href="${driveLink}" target="_blank" class="btn btn-primary btn-sm">
                                        <i class="fas fa-external-link-alt me-1"></i>
                                        ${dynamicContent.button_open_drive || 'Buka di Google Drive'}
                                    </a>
                                </p>
                            </div>
                        `;
                    } else if (driveLink) {
                        previewHTML = `
                            <div class="alert alert-secondary mt-3">
                                ${dynamicContent.preview_unavailable || 'Tidak dapat menampilkan preview.'} 
                                <a href="${driveLink}" target="_blank" class="alert-link">
                                    ${dynamicContent.preview_click_here || 'Klik di sini untuk membuka file'}
                                </a>
                            </div>
                        `;
                    }

                    $('#result').html(`
                        <div class="alert alert-success">
                            <strong>${dynamicContent.title_success || 'Sertifikat Ditemukan!'}</strong><br>
                            ${dynamicContent.label_certificate_id || 'Nomor Sertifikat:'} <b>${response.certificateID}</b><br>
                            ${dynamicContent.label_name || 'Nama Peserta:'} <b>${response.name}</b><br>
                            ${dynamicContent.label_category || 'Kategori:'} <b>${response.category}</b><br>
                            ${dynamicContent.label_status || 'Status:'} <b>${response.status_peserta}</b><br>
                            ${dynamicContent.message_success || 'Anda sudah mengikuti kegiatan.'}<br>
                            ${dynamicContent.message_contact || 'Jika ingin meminta kembali sertifikat, silakan hubungi kami.'}
                        </div>
                        ${previewHTML}
                    `);
                } 
                else if (response.status === 'invalid') {
                    $('#result').html(`
                        <div class="alert alert-danger">
                            <strong>${dynamicContent.title_invalid || 'Sertifikat Tidak Ditemukan'}</strong><br>
                            ID yang Anda masukkan: <b>${response.input || certificateID}</b><br>
                            ${dynamicContent.message_invalid || 'Silakan ikuti kegiatan terlebih dahulu.'}
                        </div>
                    `);
                } 
                else if (response.status === 'error') {
                    $('#result').html(`
                        <div class="alert alert-warning">
                            <strong>${dynamicContent.title_error || '⚠️ Terjadi Kesalahan'}</strong><br>
                            ${dynamicContent.message_error || 'Terjadi kesalahan saat memverifikasi sertifikat.'}<br>
                            <small class="text-muted">Pesan: ${response.message}</small>
                        </div>
                    `);
                } 
                else {
                    $('#result').html(`
                        <div class="alert alert-info">
                            <strong>${dynamicContent.title_unknown || 'Respons Tidak Dikenali'}</strong><br>
                            ${dynamicContent.message_unknown || 'Respons tidak dikenali dari server.'}
                        </div>
                    `);
                }
            },
            error: function(xhr, status, error) {
                console.error("XHR:", xhr.responseText);
                $('#result').html(`
                    <div class="alert alert-danger">
                        <strong>${dynamicContent.message_connection_error || 'Terjadi Kesalahan Koneksi'}</strong><br>
                        Error: ${error}<br>
                        <small class="text-muted">
                            ${xhr.responseText ? xhr.responseText.substring(0, 200) : 'Tidak ada response dari server'}
                        </small>
                    </div>
                `);
            }
        });
    });

    function getDriveEmbedLink(url) {
        if (!url) return '';
        const regexPatterns = [
            /\/d\/([a-zA-Z0-9_-]{25,})/,      // file/d/FILE_ID/
            /id=([a-zA-Z0-9_-]{25,})/,        // id=FILE_ID
            /open\?id=([a-zA-Z0-9_-]{25,})/,  // open?id=FILE_ID
            /uc\?id=([a-zA-Z0-9_-]{25,})/     // uc?id=FILE_ID
        ];
        let fileId = null;
        for (const pattern of regexPatterns) {
            const match = url.match(pattern);
            if (match && match[1]) {
                fileId = match[1];
                break;
            }
        }
        if (!fileId) return '';
        return `https://drive.google.com/file/d/${fileId}/preview`;
    }
});
```

---

## 📌 cek-sertifikat-s-proses.php: `cek-sertifikat-s-proses.php`


### Code

```php
<?php
header("Content-Type: application/json");
header("Cache-Control: no-cache, must-revalidate");
header("Expires: 0");

require_once 'database.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $certificateID = $_POST['certificateID'] ?? '';
    if (preg_match('/^YMP-S-\d+$/', $certificateID)) {
        $numericID = preg_replace('/\D/', '', $certificateID);
        try {
            $sql = "SELECT kode, nama, status, event, link, created_at 
                    FROM sertifikat 
                    WHERE kode = :kode";
            $stmt = $pdo->prepare($sql);
            $stmt->execute([':kode' => $numericID]);
            $certificate = $stmt->fetch();
            if ($certificate) {
                $result = [
                    'status' => 'valid',
                    'certificateID' => 'YMP-S-' . $certificate['kode'],
                    'name' => $certificate['nama'],
                    'category' => $certificate['event'],
                    'status_peserta' => $certificate['status'],
                    'link' => $certificate['link'],
                    'created_at' => $certificate['created_at']
                ];
                echo json_encode($result);
            } else {
                echo json_encode([
                    'status' => 'invalid',
                    'input' => $certificateID,
                    'message' => 'Sertifikat tidak ditemukan'
                ]);
            }  
        } catch (PDOException $e) {
            error_log("Database error: " . $e->getMessage());
            echo json_encode([
                'status' => 'error', 
                'message' => 'Terjadi kesalahan database'
            ]);
        }   
    } else {
        echo json_encode([
            'status' => 'invalid', 
            'message' => 'Format ID sertifikat tidak valid. Contoh: YMP-S-011711202406'
        ]);
    }
} else {
    header('HTTP/1.1 405 Method Not Allowed');
    echo json_encode(['status' => 'error', 'message' => 'Method not allowed']);
}
?>
```

---


## 📌 cek-sertifikat-seminar.php: `cek-sertifikat-seminar.php`


### Code

```php
<?php
// Debugging: Menampilkan input yang diterima
error_log(print_r($_POST, true));
// === Fetch konten dari API Node.js ===
$api_url = "http://localhost:5000/api/page-management/";
$response = @file_get_contents($api_url);

if ($response === FALSE) {
    $data = []; // fallback kosong
} else {
    $data = json_decode($response, true);
}

// === Convert hasil ke associative array ===
$content = [];
if (is_array($data)) {
    foreach ($data as $item) {
        $content[$item['field_name']] = $item['content'];
    }
}
?>

<?php
// Fetch konten dari API Node.js

?>
<?php include 'templates/header.php'; ?>
<style>
    .mfp-content {
        border-radius: 10px;
        padding: 20px;
    }

    .mfp-bg {
        background: rgba(0, 0, 0, 0.8);
    }

    .mfp-wrap {
        box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
        background: #ffffff;
    }

    .white-popup {
        position: relative;
        background: #FFF;
        padding: 20px;
        width: auto;
        max-width: 500px;
        margin: 20px auto;
        text-align: center;
        border-radius: 10px;
    }
</style>

<style>
    .embed-responsive {
        position: relative;
        padding-bottom: 56.25%;
        /* Aspect ratio 16:9 */
        height: 0;
        overflow: hidden;
        background: #000;
    }

    .embed-responsive iframe {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
    }
</style>

<style>
    .social-icon {
        font-size: 24px;
        color: #333;
        margin: 0 10px;
        transition: color 0.3s;
    }

    .social-icon:hover {
        color: #fec809;
    }
</style>
<style>
    .whatsapp-button {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 100%;
        background-color: #25D366;
        /* Warna hijau WhatsApp */
        color: white;
        border: none;
        border-radius: 5px;
        padding: 10px;
        font-size: 16px;
        cursor: pointer;
        text-decoration: none;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        transition: background-color 0.3s;
    }

    .whatsapp-button:hover {
        background-color: #128C7E;
        /* Warna hijau WhatsApp gelap */
        color: #fff;
    }

    .whatsapp-button i {
        margin-right: 10px;
    }

    /* Certificate Preview Styles */
    .certificate-preview {
        border: 2px solid #e9ecef;
        border-radius: 10px;
        padding: 20px;
        background: #f8f9fa;
    }

    .certificate-preview h5 {
        color: #495057;
        margin-bottom: 15px;
        border-bottom: 2px solid #fec809;
        padding-bottom: 8px;
    }

    .drive-actions,
    .pdf-preview .mt-2,
    .image-preview .mt-2 {
        display: flex;
        gap: 10px;
        flex-wrap: wrap;
    }

    .embed-responsive {
        border-radius: 8px;
        overflow: hidden;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }

    .embed-responsive iframe {
        border: none;
    }

    .image-preview img {
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        border: 1px solid #dee2e6;
    }

    .btn-sm {
        padding: 6px 12px;
        font-size: 14px;
    }

    /* Responsive adjustments */
    @media (max-width: 768px) {
        .certificate-preview {
            padding: 15px;
        }

        .drive-actions,
        .pdf-preview .mt-2,
        .image-preview .mt-2 {
            flex-direction: column;
        }

        .btn {
            width: 100%;
            margin-bottom: 5px;
        }
    }
</style>

<style>
    .captcha-container {
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .refresh-btn {
        background-color: transparent;
        border: none;
        cursor: pointer;
        font-size: 1.5rem;
    }

    .refresh-btn:hover {
        color: #fdc609;
    }
</style>

<?php include 'templates/nav.php'; ?>

<html>

<head>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
</head>

<body>
    <!-- Start Page Title Area -->
    <div class="page-title-area item-bg1 jarallax" data-jarallax='{"speed": 0.3}'>
        <div class="container">
            <div class="page-title-content">
                <h2><?= htmlspecialchars($content['header'] ?? 'Cek Sertifikat'); ?></h2>

                <p><?= htmlspecialchars($content['description_1'] ?? 'Selamat datang di halaman Verifikasi Sertifikat Yuk-Mari Project. Halaman ini disediakan untuk membantu Anda mengecek kevalidan dan keaslian sertifikat yang diterbitkan oleh Yuk-Mari Project, baik untuk kegiatan Seminar/Webinar/Magang/Internship/Bootcamp/CyberMeetUp/Mentor.'); ?></p>
            </div>
        </div>
    </div>
    <!-- End Page Title Area -->

    <!-- Start Blog Area -->
    <section class="blog-area ptb-70">
        <div class="container">
            <div class="row">
                <div class="container mt-5">
                    <form id="form-cek-sertifikat-seminar">
                        <div class="mb-3">
                            <?= htmlspecialchars($content['btn_label_1'] ?? 'Cek Sertifikat Seminar/Webinar/Magang/Internship/Bootcamp/Mentor:'); ?>
                            <input type="text" class="form-control" id="certificateID" placeholder=" <?= htmlspecialchars($content['input_placeholder'] ?? 'Masukkan ID Sertifikat. Contoh: YMP-S-011711202406'); ?>" required>
                        </div>
                        <button type="submit" class="btn btn-primary"><?= htmlspecialchars($content['button'] ?? 'Cek Sertifikat'); ?></button>
                    </form>
                    <div id="result" class="mt-3"></div>


                </div>
            </div>
        </div>
    </section>

    <!-- End Blog Area -->

    <?php include 'templates/footer.php'; ?>

    <script>
        function refreshCaptcha() {
            document.getElementById('captcha').src = 'lib/captcha/captcha.php?' + Math.random();
        }
    </script>

    <!-- Magnific Popup Trigger Script -->
    <script src="assets/js/virtual-classroom/popup_trigger.js"></script>

    <!-- Include JavaScript -->
    <script src="https://app.midtrans.com/snap/snap.js" data-client-key="Mid-client-a0Qdc090d4Z1OSXw"></script>
    <script src="assets/js/virtual-classroom/midtrans_listener.js"></script>

    <script src="https://www.youtube.com/iframe_api"></script>

    <!-- VIDEO BIMBEL -->
    <script src="assets/js/virtual-classroom/video_bimbel.js"></script>
    <script src="assets/js/virtual-classroom/sertifikat_cek.js"></script>
</body>

</html>
```

---


## 📌 cek-sertifikat.php: `cek-sertifikat.php`


### Code

```php
<?php
header("Content-Type: application/json");
header("Cache-Control: no-cache, must-revalidate");
header("Expires: 0");

require_once 'database.php';

try {
    $dsn = "pgsql:host=$host;port=$port;dbname=$dbname";
    $pdo = new PDO($dsn, $user, $password, [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]);
} catch (PDOException $e) {
    echo json_encode(['status' => 'error', 'message' => 'Gagal konek ke database: ' . $e->getMessage()]);
    exit;
}

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $certificateID = $_POST['certificateID'] ?? '';
    if (preg_match('/^YMP-S-\d+$/', $certificateID)) {
        $kode = preg_replace('/^YMP-S-/', '', $certificateID);
        $stmt = $pdo->prepare("SELECT * FROM sertifikat WHERE kode = :kode");
        $stmt->execute(['kode' => $kode]);
        $data = $stmt->fetch(PDO::FETCH_ASSOC);
        if ($data) {
            echo json_encode([
                'status' => 'valid',
                'certificateID' => 'YMP-S-' . $data['kode'],
                'nama' => $data['nama'],
                'event' => $data['event'],
                'hasil' => $data['status'],
                'link' => $data['link']
            ]);
        } else {
            echo json_encode([
                'status' => 'invalid',
                'input' => $certificateID,
                'message' => 'Sertifikat tidak ditemukan di database'
            ]);
        }
    } else {
        echo json_encode([
            'status' => 'invalid',
            'message' => 'Format sertifikat tidak valid (contoh: YMP-S-040701202508)'
        ]);
    }
} else {
    header('HTTP/1.1 405 Method Not Allowed');
}
```

---


## 📌 database.php: `database.php`


### Code

```php
<?php
try {
    if (file_exists(__DIR__ . '/vendor/autoload.php')) {
        require_once __DIR__ . '/vendor/autoload.php';
        
        if (file_exists(__DIR__ . '/.env')) {
            $dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
            $dotenv->load();
        }
    }
} catch (Exception $e) {
    error_log("Dotenv load error: " . $e->getMessage());
}
$host = $_ENV['DB_HOST'];
$port = $_ENV['DB_PORT'];
$dbname = $_ENV['DB_NAME'];
$user = $_ENV['DB_USER'];
$password = $_ENV['DB_PASSWORD'];

try {
    $dsn = "pgsql:host=$host;port=$port;dbname=$dbname;sslmode=require";
    
    $pdo = new PDO($dsn, $user, $password, [
        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_EMULATE_PREPARES => false,
        PDO::ATTR_TIMEOUT => 30
    ]);
    $pdo->query("SELECT 1");
    
} catch (PDOException $e) {
    error_log("Database connection failed: " . $e->getMessage());
    if (!headers_sent()) {
        header('Content-Type: application/json');
    }
    die(json_encode([
        'status' => 'error', 
        'message' => 'Database connection failed: ' . $e->getMessage()
    ]));
}
?>
```

---


## 📌 sertif-page-management.php: `sertif-page-management.php`


### Code

```php
<?php
header('Content-Type: application/json');

// Koneksi database (sesuaikan dengan koneksi Anda)
require_once 'database.php';

try {
    $sql = "SELECT field_name, content FROM sertif_page_management WHERE section = 'detail_sertifikat'";
    $stmt = $pdo->query($sql);
    $results = $stmt->fetchAll(PDO::FETCH_ASSOC);
    
    $contentMap = [];
    foreach ($results as $row) {
        $contentMap[$row['field_name']] = $row['content'];
    }
    
    echo json_encode($contentMap);
    
} catch (PDOException $e) {
    // Return empty object jika error
    echo json_encode(new stdClass());
}
?>
```

---


## 📌 sertifikat.php: `sertifikat.php`


### Code

```php
<?php
require('lib/fpdf/fpdf.php');
    
class PDF extends FPDF
{
    // Fungsi untuk menampilkan konten sertifikat
    function CertificateBody($nama, $judul_kursus, $background, $tanggal, $id_sertifikat, $logo, $cap)
    {
        // Tambahkan gambar background
        $this->Image($background, 0, 0, $this->GetPageWidth(), $this->GetPageHeight());
        
        // Atur posisi untuk menambahkan teks di atas gambar
        $this->SetXY(0, 81); // Atur posisi Y sesuai kebutuhan
        $this->SetFont('Arial', '', 35);
        $this->Cell(298, 10, $nama, 0, 1, 'C');

        $this->SetXY(0, 110); // Atur posisi Y sesuai kebutuhan
        $this->SetFont('Arial', '', 15);
        $this->MultiCell(298, 7, 'Dalam menyelesaikan Kursus ' . $judul_kursus . ' ' . "\n" . 'dan menunjukan pemahaman tentang modul dan persyaratan kursusnya', 0, 'C');
            
        $this->SetXY(0, 135); // Atur posisi Y sesuai kebutuhan
        $this->SetFont('Arial', 'B', 15);
        $this->MultiCell(320, 7, 'Bandung, '.$tanggal, 0, 'C');
        
        $this->SetXY(0, 10); // Atur posisi Y sesuai kebutuhan
        $this->SetFont('Arial', 'B', 7);
        $this->MultiCell(135, 7, 'ID Sertifikat: YMP-S-'.$id_sertifikat, 0, 'C');
        
        // Tambahkan gambar logo di bawah teks
        $this->Image($logo, 120, 135, 70); // ('file', x, y, width)

            // Tambahkan gambar logo di bawah teks
        $this->Image($cap, 130, 75, 30); // ('file', x, y, width)
    }
}

// Pastikan library JWT di-load
require_once 'lib/PHP-JWT/src/JWT.php'; // Path ke JWT library
require_once 'lib/PHP-JWT/src/ExpiredException.php'; // Path ke JWT library
require_once 'lib/PHP-JWT/src/Key.php'; // Path ke Key library
    
use \Firebase\JWT\JWT;
use \Firebase\JWT\Key;

// Ambil token dari query string
    $token = isset($_GET['token']) ? $_GET['token'] : null;

    // Ambil jawaban dari form yang dikirim
    $jawaban = isset($_POST['jawaban']) ? $_POST['jawaban'] : [];
    
    // Inisialisasi array untuk menyimpan pesan kesalahan
    $errors = [];
    
    // Kunci rahasia yang sama dengan yang digunakan untuk menghasilkan JWT
    $secretKey = 'C0fnR!23C9W*ox';
    
$jwtToken = $_GET['token'];

// Decode dan verifikasi token
$decoded = JWT::decode($jwtToken, new Key($secretKey, 'HS256'));
$decoded_array = (array) $decoded;

// Nama file yang akan dicek
        $filename = 'id-sertifikat.txt';
        
        // Data yang akan dimasukkan ke sertifikat
$nama = $decoded_array['nama_lengkap'] ? $decoded_array['nama_lengkap'] : 'John Doe';
$judul_kursus = $decoded_array['judul_kursus'] ? $decoded_array['judul_kursus'] : null;
$id_sertifikat = $decoded_array['transaction_id'] ? $decoded_array['transaction_id'] : null;
        
        // Teks yang ingin ditambahkan
        $textToAdd = 'YMP-S-' . $id_sertifikat . '-' . $nama . '-' . $judul_kursus . '' .PHP_EOL .'';
        
        // Baca seluruh isi file
        $fileContents = '';
        if (file_exists($filename)) {
            $fileContents = file_get_contents($filename);
        }
        
        // Cek jika data sudah ada
        if (strpos($fileContents, $textToAdd) === false) {
            // Data tidak ada dalam file, jadi tambahkan
            $file = fopen($filename, 'a');
            if ($file) {
                fwrite($file, $textToAdd);
                fclose($file);
            } else {
                echo "Tidak dapat membuka file.";
            }
        } else {
            echo null;
        }
        
// Inisialisasi PDF
$pdf = new PDF();

// Set ukuran halaman ke A4
$pdf->AddPage('L', 'A4'); // 'P' untuk portrait, 'A4' untuk ukuran A4

$bulanIndonesia = array(
    1 => 'Januari',
    2 => 'Februari',
    3 => 'Maret',
    4 => 'April',
    5 => 'Mei',
    6 => 'Juni',
    7 => 'Juli',
    8 => 'Agustus',
    9 => 'September',
    10 => 'Oktober',
    11 => 'November',
    12 => 'Desember'
);

// Ambil hari, bulan, dan tahun saat ini
$hari = date('d');
$bulan = date('n'); // Menggunakan 'n' untuk mendapatkan angka bulan tanpa leading zero
$tahun = date('Y');

$background = 'sertifikat.png'; // Nama file gambar dari Canva
$tanggal = $tanggal = $hari . ' ' . $bulanIndonesia[$bulan] . ' ' . $tahun;
$logo = 'TTD.png'; // Nama file gambar logo
$cap = 'cap_lulus.png';

// Membuat sertifikat
$pdf->CertificateBody($nama, $judul_kursus, $background, $tanggal, $id_sertifikat, $logo, $cap);

// Output PDF
$pdf->Output('I', 'certificate.pdf');
?>
```


## 🗄️ Database Structure

Dokumentasi struktur database untuk modul **Sertifikat**.


### 📁 Tabel: `detail_sertif_management`

| Kolom      | Tipe Data    | Not Null | Default                                    | Keterangan       |
| ---------- | ------------ | -------- | ------------------------------------------ | ---------------- |
| id         | serial4      | true     | nextval('detail_sertif_management_id_seq') | Primary key      |
| field_name | varchar(100) | true     | NULL                                       | Nama field       |
| content    | text         | true     | NULL                                       | Isi konten       |
| created_at | timestamp    | false    | CURRENT_TIMESTAMP                          | Timestamp dibuat |
| updated_at | timestamp    | false    | CURRENT_TIMESTAMP                          | Timestamp update |

### 📁 Tabel: `sertif_page_management`

| Kolom      | Tipe Data    | Not Null | Default                                  | Keterangan       |
| ---------- | ------------ | -------- | ---------------------------------------- | ---------------- |
| id         | serial4      | true     | nextval('sertif_page_management_id_seq') | Primary key      |
| section    | varchar(100) | true     | NULL                                     | Nama section     |
| field_name | varchar(100) | true     | NULL                                     | Nama field       |
| content    | text         | true     | NULL                                     | Isi konten       |
| created_at | timestamp    | false    | CURRENT_TIMESTAMP                        | Timestamp dibuat |
| updated_at | timestamp    | false    | CURRENT_TIMESTAMP                        | Timestamp update |

### 📁 Tabel: `sertifikat`

| Kolom      | Tipe Data    | Not Null | Default                      | Keterangan              |
| ---------- | ------------ | -------- | ---------------------------- | ----------------------- |
| id         | serial4      | true     | nextval('sertifikat_id_seq') | Primary key             |
| kode       | varchar(50)  | true     | NULL                         | Kode sertifikat unik    |
| nama       | varchar(100) | true     | NULL                         | Nama pemilik / penerima |
| status     | varchar(20)  | true     | NULL                         | Status sertifikat       |
| event      | varchar(100) | true     | NULL                         | Nama event              |
| link       | varchar(255) | true     | NULL                         | URL file sertifikat     |
| created_at | timestamp    | false    | CURRENT_TIMESTAMP            | Timestamp dibuat        |

---

---

