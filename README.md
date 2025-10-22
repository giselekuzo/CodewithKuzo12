"use client"

import { useAuth } from "@/context/auth-context"
import { useRouter } from "next/navigation"
import { useEffect, useState } from "react"
import { isAdmin } from "@/lib/admin-data"
import { AdminSidebar } from "@/components/admin-sidebar"
import { Button } from "@/components/ui/button"
import Link from "next/link"

interface AdminUserData {
  id: string
  email: string
  username: string
  role: string
  createdAt: string
}

export default function AdminUsersPage() {
  const { user } = useAuth()
  const router = useRouter()
  const [users, setUsers] = useState<AdminUserData[]>([])

  useEffect(() => {
    if (!user) {
      router.push("/login")
    } else if (!isAdmin(user.id)) {
      router.push("/dashboard")
    } else {
      const storedUsers = JSON.parse(localStorage.getItem("users") || "[]")
      setUsers(storedUsers)
    }
  }, [user, router])

  if (!user || !isAdmin(user.id)) return null

  return (
    <main className="min-h-screen bg-gradient-to-br from-slate-950 via-slate-900 to-slate-950 text-white flex">
      <AdminSidebar />

      <div className="flex-1 flex flex-col">
        {/* Navigation */}
        <nav className="flex items-center justify-between px-6 py-4 border-b border-slate-800">
          <Link href="/dashboard" className="flex items-center gap-2">
            <div className="w-8 h-8 bg-gradient-to-br from-blue-500 to-cyan-500 rounded-lg flex items-center justify-center font-bold">
              CK
            </div>
            <span className="text-xl font-bold">CodeWithKuzo</span>
          </Link>
        </nav>

        {/* Content */}
        <section className="flex-1 p-6">
          <div className="space-y-6">
            <div>
              <h1 className="text-4xl font-bold mb-2">User Management</h1>
              <p className="text-slate-400">Manage platform users</p>
            </div>

            {/* Users Table */}
            <div className="rounded-lg overflow-hidden border border-slate-700">
              <table className="w-full">
                <thead>
                  <tr className="bg-slate-800/50 border-b border-slate-700">
                    <th className="px-4 py-3 text-left text-slate-300 font-semibold">Username</th>
                    <th className="px-4 py-3 text-left text-slate-300 font-semibold">Email</th>
                    <th className="px-4 py-3 text-left text-slate-300 font-semibold">Role</th>
                    <th className="px-4 py-3 text-left text-slate-300 font-semibold">Joined</th>
                    <th className="px-4 py-3 text-left text-slate-300 font-semibold">Actions</th>
                  </tr>
                </thead>
                <tbody>
                  {users.map((u) => (
                    <tr key={u.id} className="border-b border-slate-700 hover:bg-slate-800/30 transition">
                      <td className="px-4 py-3 text-white font-semibold">{u.username}</td>
                      <td className="px-4 py-3 text-slate-400">{u.email}</td>
                      <td className="px-4 py-3">
                        <span
                          className={`px-2 py-1 rounded text-xs font-semibold ${
                            u.role === "admin" ? "bg-purple-500/20 text-purple-300" : "bg-slate-700 text-slate-300"
                          }`}
                        >
                          {u.role || "user"}
                        </span>
                      </td>
                      <td className="px-4 py-3 text-slate-400">{new Date(u.createdAt).toLocaleDateString()}</td>
                      <td className="px-4 py-3">
                        <div className="flex gap-2">
                          <Button
                            variant="outline"
                            size="sm"
                            className="border-slate-600 text-slate-300 bg-transparent"
                          >
                            View
                          </Button>
                          <Button
                            variant="outline"
                            size="sm"
                            className="border-slate-600 text-slate-300 bg-transparent"
                          >
                            Edit
                          </Button>
                        </div>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </div>
        </section>
      </div>
    </main>
  )
}
