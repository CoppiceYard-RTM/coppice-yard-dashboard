import React, { useState, useMemo } from 'react';
import { Calendar, Users, FileText, CheckCircle, AlertCircle, Plus, Edit2, Trash2, Eye, EyeOff, LogOut, Home, Settings, Menu, X } from 'lucide-react';

export default function RTMDashboard() {
  const [authenticated, setAuthenticated] = useState(false);
  const [credentials, setCredentials] = useState({ username: '', password: '' });
  const [activeTab, setActiveTab] = useState('dashboard');
  const [sidebarOpen, setSidebarOpen] = useState(true);
  const [showPassword, setShowPassword] = useState(false);

  // Sample data - in production, this would come from a backend
  const [services, setServices] = useState([
    { id: 1, name: 'Swimming Pool', frequency: 'Weekly', contractor: 'Aqua Care Ltd', status: 'Active', nextService: '2025-05-16' },
    { id: 2, name: 'Lift Maintenance', frequency: 'Quarterly', contractor: 'Lift Solutions', status: 'Active', nextService: '2025-05-20' },
    { id: 3, name: 'Fire Safety Checks', frequency: 'Monthly', contractor: 'FireSafe UK', status: 'Active', nextService: '2025-05-15' },
    { id: 4, name: 'Gas Boiler Service', frequency: 'Annual', contractor: 'Heat Pro Services', status: 'Active', nextService: '2025-06-01' },
  ]);

  const [contractors, setContractors] = useState([
    { id: 1, name: 'Aqua Care Ltd', service: 'Swimming Pool', contact: '020 1234 5678', email: 'contact@aquacare.co.uk', status: 'Active' },
    { id: 2, name: 'Lift Solutions', service: 'Lift Maintenance', contact: '020 8765 4321', email: 'info@liftsolutions.co.uk', status: 'Active' },
    { id: 3, name: 'FireSafe UK', service: 'Fire Safety', contact: '020 5555 1111', email: 'safety@firesafe.uk', status: 'Active' },
    { id: 4, name: 'Heat Pro Services', service: 'Boiler Service', contact: '020 2222 3333', email: 'support@heatpro.co.uk', status: 'Active' },
  ]);

  const [contracts, setContracts] = useState([
    { id: 1, contractor: 'Aqua Care Ltd', service: 'Swimming Pool', startDate: '2024-01-15', endDate: '2025-01-14', value: '£2,500', status: 'Active', renewal: '60 days' },
    { id: 2, contractor: 'Lift Solutions', service: 'Lift Maintenance', startDate: '2023-06-01', endDate: '2026-05-31', value: '£5,200', status: 'Active', renewal: '400 days' },
    { id: 3, contractor: 'FireSafe UK', service: 'Fire Safety', startDate: '2024-03-20', endDate: '2025-03-19', value: '£1,800', status: 'Active', renewal: '23 days' },
  ]);

  const [compliance, setCompliance] = useState([
    { id: 1, item: 'Fire Risk Assessment', dueDate: '2025-06-15', status: 'Compliant', certificate: 'FRA-2024-001.pdf', lastChecked: '2024-06-15' },
    { id: 2, item: 'Electrical Safety (EICR)', dueDate: '2025-01-20', status: 'Overdue', certificate: 'EICR-2023-001.pdf', lastChecked: '2023-01-20' },
    { id: 3, item: 'Gas Safety Certificate', dueDate: '2025-04-10', status: 'Compliant', certificate: 'GAS-2024-001.pdf', lastChecked: '2024-04-10' },
    { id: 4, item: 'Lift Safety Inspection', dueDate: '2025-07-01', status: 'Compliant', certificate: 'LIFT-2024-001.pdf', lastChecked: '2024-07-01' },
    { id: 5, item: 'Building Insurance Renewal', dueDate: '2025-05-30', status: 'Pending', certificate: 'INS-2024-001.pdf', lastChecked: '2024-05-30' },
  ]);

  // Authentication handler
  const handleLogin = () => {
    if (credentials.username && credentials.password) {
      setAuthenticated(true);
      setCredentials({ username: '', password: '' });
    }
  };

  // Calculate upcoming actions
  const upcomingServices = useMemo(() => {
    const now = new Date();
    return services.filter(s => {
      const nextDate = new Date(s.nextService);
      return (nextDate - now) < (14 * 24 * 60 * 60 * 1000); // Next 2 weeks
    }).length;
  }, [services]);

  const overdueCompliance = useMemo(() => {
    return compliance.filter(c => c.status === 'Overdue').length;
  }, [compliance]);

  const expiringSoon = useMemo(() => {
    return contracts.filter(c => c.renewal.includes('day') && !c.renewal.includes('400')).length;
  }, [contracts]);

  if (!authenticated) {
    return (
      <div className="min-h-screen bg-gradient-to-br from-slate-900 via-slate-800 to-slate-900 flex items-center justify-center p-4">
        <style>{`
          @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;700&family=IBM+Plex+Mono:wght@400;600&display=swap');
          * { font-family: 'Outfit', sans-serif; }
          .login-card { background: linear-gradient(135deg, rgba(30, 41, 59, 0.8) 0%, rgba(15, 23, 42, 0.9) 100%); border: 1px solid rgba(71, 85, 105, 0.4); }
        `}</style>
        
        <div className="w-full max-w-md">
          <div className="login-card backdrop-blur-xl rounded-2xl p-8 shadow-2xl">
            <div className="flex justify-center mb-8">
              <div className="w-12 h-12 bg-gradient-to-br from-cyan-400 to-blue-500 rounded-xl flex items-center justify-center">
                <Home className="text-white" size={24} />
              </div>
            </div>
            <h1 className="text-3xl font-bold text-white text-center mb-2">Coppice Yard</h1>
            <p className="text-slate-400 text-center text-sm mb-8">RTM Management System</p>
            
            <div className="space-y-5">
              <div>
                <label className="block text-slate-300 text-sm font-medium mb-2">Username</label>
                <input
                  type="text"
                  placeholder="admin"
                  value={credentials.username}
                  onChange={(e) => setCredentials({...credentials, username: e.target.value})}
                  onKeyPress={(e) => e.key === 'Enter' && handleLogin()}
                  className="w-full bg-slate-700/50 border border-slate-600 rounded-lg px-4 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-cyan-400 transition"
                />
              </div>
              
              <div>
                <label className="block text-slate-300 text-sm font-medium mb-2">Password</label>
                <div className="relative">
                  <input
                    type={showPassword ? 'text' : 'password'}
                    placeholder="••••••••"
                    value={credentials.password}
                    onChange={(e) => setCredentials({...credentials, password: e.target.value})}
                    onKeyPress={(e) => e.key === 'Enter' && handleLogin()}
                    className="w-full bg-slate-700/50 border border-slate-600 rounded-lg px-4 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-cyan-400 transition"
                  />
                  <button
                    onClick={() => setShowPassword(!showPassword)}
                    className="absolute right-3 top-3 text-slate-400 hover:text-cyan-400 transition"
                  >
                    {showPassword ? <EyeOff size={18} /> : <Eye size={18} />}
                  </button>
                </div>
              </div>

              <button
                onClick={handleLogin}
                className="w-full bg-gradient-to-r from-cyan-400 to-blue-500 hover:from-cyan-300 hover:to-blue-400 text-slate-900 font-semibold py-3 rounded-lg transition transform hover:scale-105 mt-6"
              >
                Sign In
              </button>
            </div>

            <p className="text-slate-500 text-center text-xs mt-8 border-t border-slate-700 pt-8">
              Demo credentials: <br/><span className="text-slate-400 font-mono">admin / password</span>
            </p>
          </div>
        </div>
      </div>
    );
  }

  // Main Dashboard
  return (
    <div className="flex h-screen bg-slate-950 text-slate-100">
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;700&family=IBM+Plex+Mono:wght@400;600&display=swap');
        * { font-family: 'Outfit', sans-serif; }
        
        .stat-card { background: linear-gradient(135deg, rgba(30, 41, 59, 0.5) 0%, rgba(15, 23, 42, 0.7) 100%); border: 1px solid rgba(71, 85, 105, 0.3); }
        .tab-active { border-b-2 border-cyan-400; color: white; }
        .tab-inactive { border-b-2 border-transparent; color: rgb(100, 116, 139); }
        .btn-primary { background: linear-gradient(135deg, #06b6d4 0%, #0ea5e9 100%); }
        .table-row { border-b: 1px solid rgba(71, 85, 105, 0.2); }
        .status-badge { display: inline-block; padding: 4px 10px; border-radius: 6px; font-size: 12px; font-weight: 600; }
        .status-active { background: rgba(34, 197, 94, 0.15); color: #86efac; }
        .status-pending { background: rgba(234, 179, 8, 0.15); color: #fcd34d; }
        .status-overdue { background: rgba(239, 68, 68, 0.15); color: #fca5a5; }
        .status-compliant { background: rgba(34, 197, 94, 0.15); color: #86efac; }
        
        .sidebar { transition: width 0.3s ease; }
        .sidebar-item { padding: 12px 16px; margin: 8px 12px; border-radius: 8px; cursor: pointer; transition: all 0.2s; }
        .sidebar-item.active { background: rgba(6, 182, 212, 0.15); color: #06b6d4; }
        .sidebar-item:hover { background: rgba(71, 85, 105, 0.3); }
      `}</style>

      {/* Sidebar */}
      <div className={`sidebar ${sidebarOpen ? 'w-64' : 'w-20'} bg-slate-900 border-r border-slate-800 flex flex-col`}>
        <div className="p-6 flex items-center justify-between">
          {sidebarOpen && <h2 className="font-bold text-cyan-400">Coppice Yard</h2>}
          <button onClick={() => setSidebarOpen(!sidebarOpen)} className="text-slate-400 hover:text-cyan-400">
            {sidebarOpen ? <X size={20} /> : <Menu size={20} />}
          </button>
        </div>

        <nav className="flex-1">
          {[
            { icon: Home, label: 'Dashboard', id: 'dashboard' },
            { icon: FileText, label: 'Services', id: 'services' },
            { icon: Users, label: 'Contractors', id: 'contractors' },
            { icon: Calendar, label: 'Contracts', id: 'contracts' },
            { icon: CheckCircle, label: 'Compliance', id: 'compliance' },
            { icon: Settings, label: 'Settings', id: 'settings' },
          ].map(({ icon: Icon, label, id }) => (
            <div
              key={id}
              onClick={() => setActiveTab(id)}
              className={`sidebar-item ${activeTab === id ? 'active' : ''} flex items-center gap-3`}
            >
              <Icon size={20} />
              {sidebarOpen && <span>{label}</span>}
            </div>
          ))}
        </nav>

        <div className="p-6 border-t border-slate-800">
          <button
            onClick={() => setAuthenticated(false)}
            className="flex items-center gap-3 text-slate-400 hover:text-red-400 w-full text-sm"
          >
            <LogOut size={20} />
            {sidebarOpen && 'Logout'}
          </button>
        </div>
      </div>

      {/* Main Content */}
      <div className="flex-1 flex flex-col overflow-hidden">
        <div className="border-b border-slate-800 p-6 flex justify-between items-center">
          <h1 className="text-2xl font-bold">
            {activeTab === 'dashboard' && 'Dashboard'}
            {activeTab === 'services' && 'Services & Maintenance'}
            {activeTab === 'contractors' && 'Contractors Directory'}
            {activeTab === 'contracts' && 'Contract Management'}
            {activeTab === 'compliance' && 'Compliance & Certificates'}
            {activeTab === 'settings' && 'Settings'}
          </h1>
        </div>

        <div className="flex-1 overflow-auto p-6">
          {/* Dashboard Tab */}
          {activeTab === 'dashboard' && (
            <div className="space-y-6">
              <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
                <div className="stat-card p-6 rounded-xl">
                  <div className="flex justify-between items-start">
                    <div>
                      <p className="text-slate-400 text-sm mb-2">Active Services</p>
                      <p className="text-3xl font-bold">{services.length}</p>
                    </div>
                    <FileText className="text-cyan-400" size={24} />
                  </div>
                </div>

                <div className="stat-card p-6 rounded-xl">
                  <div className="flex justify-between items-start">
                    <div>
                      <p className="text-slate-400 text-sm mb-2">Upcoming Services</p>
                      <p className="text-3xl font-bold text-amber-400">{upcomingServices}</p>
                    </div>
                    <AlertCircle className="text-amber-400" size={24} />
                  </div>
                </div>

                <div className="stat-card p-6 rounded-xl">
                  <div className="flex justify-between items-start">
                    <div>
                      <p className="text-slate-400 text-sm mb-2">Compliance Issues</p>
                      <p className="text-3xl font-bold text-red-400">{overdueCompliance}</p>
                    </div>
                    <AlertCircle className="text-red-400" size={24} />
                  </div>
                </div>

                <div className="stat-card p-6 rounded-xl">
                  <div className="flex justify-between items-start">
                    <div>
                      <p className="text-slate-400 text-sm mb-2">Contracts Expiring</p>
                      <p className="text-3xl font-bold text-orange-400">{expiringSoon}</p>
                    </div>
                    <Calendar className="text-orange-400" size={24} />
                  </div>
                </div>
              </div>

              <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
                {/* Next Services */}
                <div className="stat-card p-6 rounded-xl">
                  <h3 className="font-bold mb-4 text-lg">Next Services Due</h3>
                  <div className="space-y-3">
                    {services.slice(0, 3).map(service => (
                      <div key={service.id} className="flex justify-between items-center pb-3 border-b border-slate-700 last:border-0">
                        <div>
                          <p className="font-medium text-sm">{service.name}</p>
                          <p className="text-xs text-slate-400">{service.contractor}</p>
                        </div>
                        <span className="text-xs text-cyan-400">{service.nextService}</span>
                      </div>
                    ))}
                  </div>
                </div>

                {/* Compliance Status */}
                <div className="stat-card p-6 rounded-xl">
                  <h3 className="font-bold mb-4 text-lg">Compliance Status</h3>
                  <div className="space-y-3">
                    {compliance.slice(0, 3).map(item => (
                      <div key={item.id} className="flex justify-between items-center pb-3 border-b border-slate-700 last:border-0">
                        <div className="flex-1">
                          <p className="font-medium text-sm">{item.item}</p>
                          <p className="text-xs text-slate-400">Due: {item.dueDate}</p>
                        </div>
                        <span className={`status-badge status-${item.status.toLowerCase()}`}>
                          {item.status}
                        </span>
                      </div>
                    ))}
                  </div>
                </div>
              </div>
            </div>
          )}

          {/* Services Tab */}
          {activeTab === 'services' && (
            <div className="space-y-4">
              <button className="btn-primary text-white px-4 py-2 rounded-lg font-medium flex items-center gap-2 hover:shadow-lg transition">
                <Plus size={18} /> Add Service
              </button>
              
              <div className="overflow-x-auto stat-card rounded-xl">
                <table className="w-full text-sm">
                  <thead>
                    <tr className="border-b border-slate-700">
                      <th className="px-6 py-3 text-left font-semibold">Service</th>
                      <th className="px-6 py-3 text-left font-semibold">Contractor</th>
                      <th className="px-6 py-3 text-left font-semibold">Frequency</th>
                      <th className="px-6 py-3 text-left font-semibold">Next Service</th>
                      <th className="px-6 py-3 text-left font-semibold">Status</th>
                      <th className="px-6 py-3 text-center font-semibold">Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    {services.map(service => (
                      <tr key={service.id} className="table-row hover:bg-slate-800/30 transition">
                        <td className="px-6 py-4">{service.name}</td>
                        <td className="px-6 py-4 text-slate-300">{service.contractor}</td>
                        <td className="px-6 py-4 text-slate-300">{service.frequency}</td>
                        <td className="px-6 py-4 text-slate-300">{service.nextService}</td>
                        <td className="px-6 py-4">
                          <span className="status-badge status-active">{service.status}</span>
                        </td>
                        <td className="px-6 py-4 text-center">
                          <button className="text-cyan-400 hover:text-cyan-300 mr-3"><Edit2 size={16} /></button>
                          <button className="text-red-400 hover:text-red-300"><Trash2 size={16} /></button>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          )}

          {/* Contractors Tab */}
          {activeTab === 'contractors' && (
            <div className="space-y-4">
              <button className="btn-primary text-white px-4 py-2 rounded-lg font-medium flex items-center gap-2 hover:shadow-lg transition">
                <Plus size={18} /> Add Contractor
              </button>

              <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                {contractors.map(contractor => (
                  <div key={contractor.id} className="stat-card p-6 rounded-xl">
                    <div className="flex justify-between items-start mb-4">
                      <div className="flex-1">
                        <h3 className="font-bold text-base mb-1">{contractor.name}</h3>
                        <p className="text-sm text-slate-400">{contractor.service}</p>
                      </div>
                      <span className="status-badge status-active text-xs">{contractor.status}</span>
                    </div>
                    
                    <div className="space-y-2 text-sm mb-4 py-4 border-y border-slate-700">
                      <p className="text-slate-300">📞 {contractor.contact}</p>
                      <p className="text-slate-300">✉️ {contractor.email}</p>
                    </div>
                    
                    <div className="flex gap-2">
                      <button className="flex-1 text-cyan-400 hover:text-cyan-300 py-2 text-sm font-medium"><Edit2 size={14} className="inline mr-1" /> Edit</button>
                      <button className="flex-1 text-red-400 hover:text-red-300 py-2 text-sm font-medium"><Trash2 size={14} className="inline mr-1" /> Remove</button>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          )}

          {/* Contracts Tab */}
          {activeTab === 'contracts' && (
            <div className="space-y-4">
              <button className="btn-primary text-white px-4 py-2 rounded-lg font-medium flex items-center gap-2 hover:shadow-lg transition">
                <Plus size={18} /> New Contract
              </button>

              <div className="overflow-x-auto stat-card rounded-xl">
                <table className="w-full text-sm">
                  <thead>
                    <tr className="border-b border-slate-700">
                      <th className="px-6 py-3 text-left font-semibold">Contractor</th>
                      <th className="px-6 py-3 text-left font-semibold">Service</th>
                      <th className="px-6 py-3 text-left font-semibold">Period</th>
                      <th className="px-6 py-3 text-left font-semibold">Value</th>
                      <th className="px-6 py-3 text-left font-semibold">Renewal</th>
                      <th className="px-6 py-3 text-left font-semibold">Status</th>
                      <th className="px-6 py-3 text-center font-semibold">Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    {contracts.map(contract => (
                      <tr key={contract.id} className="table-row hover:bg-slate-800/30 transition">
                        <td className="px-6 py-4 font-medium">{contract.contractor}</td>
                        <td className="px-6 py-4 text-slate-300">{contract.service}</td>
                        <td className="px-6 py-4 text-slate-300 text-xs">{contract.startDate} → {contract.endDate}</td>
                        <td className="px-6 py-4 font-semibold">{contract.value}</td>
                        <td className="px-6 py-4 text-slate-300">{contract.renewal}</td>
                        <td className="px-6 py-4">
                          <span className="status-badge status-active">{contract.status}</span>
                        </td>
                        <td className="px-6 py-4 text-center">
                          <button className="text-cyan-400 hover:text-cyan-300 mr-3"><Edit2 size={16} /></button>
                          <button className="text-red-400 hover:text-red-300"><Trash2 size={16} /></button>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          )}

          {/* Compliance Tab */}
          {activeTab === 'compliance' && (
            <div className="space-y-4">
              <button className="btn-primary text-white px-4 py-2 rounded-lg font-medium flex items-center gap-2 hover:shadow-lg transition">
                <Plus size={18} /> Add Compliance Item
              </button>

              <div className="overflow-x-auto stat-card rounded-xl">
                <table className="w-full text-sm">
                  <thead>
                    <tr className="border-b border-slate-700">
                      <th className="px-6 py-3 text-left font-semibold">Compliance Item</th>
                      <th className="px-6 py-3 text-left font-semibold">Due Date</th>
                      <th className="px-6 py-3 text-left font-semibold">Status</th>
                      <th className="px-6 py-3 text-left font-semibold">Certificate</th>
                      <th className="px-6 py-3 text-left font-semibold">Last Checked</th>
                      <th className="px-6 py-3 text-center font-semibold">Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    {compliance.map(item => (
                      <tr key={item.id} className="table-row hover:bg-slate-800/30 transition">
                        <td className="px-6 py-4 font-medium">{item.item}</td>
                        <td className="px-6 py-4 text-slate-300">{item.dueDate}</td>
                        <td className="px-6 py-4">
                          <span className={`status-badge status-${item.status.toLowerCase()}`}>
                            {item.status}
                          </span>
                        </td>
                        <td className="px-6 py-4 text-cyan-400 hover:text-cyan-300 cursor-pointer text-xs">{item.certificate}</td>
                        <td className="px-6 py-4 text-slate-300">{item.lastChecked}</td>
                        <td className="px-6 py-4 text-center">
                          <button className="text-cyan-400 hover:text-cyan-300 mr-3"><Edit2 size={16} /></button>
                          <button className="text-red-400 hover:text-red-300"><Trash2 size={16} /></button>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          )}

          {/* Settings Tab */}
          {activeTab === 'settings' && (
            <div className="stat-card p-6 rounded-xl max-w-2xl">
              <h2 className="text-xl font-bold mb-6">Settings</h2>
              <div className="space-y-4">
                <div>
                  <label className="block text-sm font-medium mb-2">Property Name</label>
                  <input type="text" placeholder="E.g. Oakwood Estate" className="w-full bg-slate-700/50 border border-slate-600 rounded px-4 py-2 text-white focus:border-cyan-400 outline-none" />
                </div>
                <div>
                  <label className="block text-sm font-medium mb-2">Property Address</label>
                  <input type="text" placeholder="Full address" className="w-full bg-slate-700/50 border border-slate-600 rounded px-4 py-2 text-white focus:border-cyan-400 outline-none" />
                </div>
                <div>
                  <label className="block text-sm font-medium mb-2">Responsible Person</label>
                  <input type="text" placeholder="Name" className="w-full bg-slate-700/50 border border-slate-600 rounded px-4 py-2 text-white focus:border-cyan-400 outline-none" />
                </div>
                <button className="btn-primary text-white px-6 py-2 rounded-lg font-medium mt-6">Save Settings</button>
              </div>
            </div>
          )}
        </div>
      </div>
    </div>
  );
}
