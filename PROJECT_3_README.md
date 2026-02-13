# Task Management - Analytics Dashboard

![React](https://img.shields.io/badge/React-18-blue?style=flat&logo=react)
![TypeScript](https://img.shields.io/badge/TypeScript-5-blue?style=flat&logo=typescript)
![Chart.js](https://img.shields.io/badge/Chart.js-4-red?style=flat&logo=chartdotjs)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)

A comprehensive analytics and reporting dashboard for the Task Management System, providing real-time insights into team performance, task metrics, and project progress.

## 📊 Overview

This dashboard provides stakeholders with actionable insights through:
- Real-time task completion metrics
- Team productivity analytics
- Individual performance tracking
- Project timeline visualization
- Custom report generation
- Data export capabilities

## ✨ Features

### Analytics Modules

- 📈 **Task Metrics**
  - Completion rates by time period
  - Status distribution (To Do, In Progress, Done)
  - Priority breakdown
  - Overdue task tracking

- 👥 **Team Performance**
  - Workload distribution across team members
  - Average completion time per assignee
  - Task assignment trends
  - Team velocity tracking

- ⏱️ **Time Analytics**
  - Time spent per task
  - Time-to-completion trends
  - Deadline adherence rates
  - Burndown charts

- 🎯 **Project Insights**
  - Project progress tracking
  - Milestone achievement rates
  - Bottleneck identification
  - Resource utilization

### Dashboard Features

- 🔄 **Real-time Updates**: WebSocket integration for live data
- 📅 **Date Range Filtering**: Custom date ranges and quick filters
- 📊 **Interactive Charts**: Drill-down capabilities
- 📱 **Responsive Design**: Works on desktop, tablet, and mobile
- 🎨 **Customizable Widgets**: Drag-and-drop dashboard customization
- 📥 **Export Options**: PDF, Excel, CSV export
- 🔔 **Alert Configuration**: Set alerts for critical metrics

## 🛠️ Tech Stack

### Frontend
- **React 18** with Hooks and Context API
- **TypeScript** for type safety
- **Chart.js** & **Recharts** for data visualization
- **Material-UI (MUI)** for components
- **React Query** for data fetching
- **React Router** for navigation
- **Axios** for API calls

### Additional Libraries
- **date-fns** for date manipulation
- **xlsx** for Excel export
- **jspdf** for PDF generation
- **socket.io-client** for WebSocket
- **react-beautiful-dnd** for drag-and-drop

## 🚀 Quick Start

### Prerequisites

- Node.js 18+ and npm/yarn
- Task Management System backend running
- Modern web browser

### Installation

```bash
# Clone repository
git clone https://github.com/YOUR_USERNAME/task-management-analytics-dashboard.git
cd task-management-analytics-dashboard

# Install dependencies
npm install
# or
yarn install

# Create environment file
cp .env.example .env
# Edit .env with your API endpoint

# Start development server
npm start
# or
yarn start
```

The dashboard will open at `http://localhost:3000`

## ⚙️ Configuration

### .env

```env
# API Configuration
REACT_APP_API_URL=http://localhost:8080/api
REACT_APP_WS_URL=ws://localhost:8080/ws

# Feature Flags
REACT_APP_ENABLE_EXPORT=true
REACT_APP_ENABLE_REAL_TIME=true

# Analytics
REACT_APP_GA_TRACKING_ID=UA-XXXXXXXXX-X
```

## 📸 Screenshots

### Main Dashboard
![Main Dashboard](docs/images/dashboard-main.png)
*Overview with key metrics and charts*

### Team Performance
![Team Performance](docs/images/team-performance.png)
*Team productivity and workload distribution*

### Task Analytics
![Task Analytics](docs/images/task-analytics.png)
*Detailed task metrics and trends*

### Custom Reports
![Custom Reports](docs/images/custom-reports.png)
*Report builder with export options*

## 📊 Dashboard Widgets

### 1. Key Metrics Cards
```typescript
interface MetricCard {
  title: string;
  value: number;
  change: number;         // Percentage change from previous period
  trend: 'up' | 'down';
  icon: ReactNode;
}

// Example: Total Tasks, Completed Tasks, Overdue Tasks, Average Completion Time
```

### 2. Task Status Distribution (Pie Chart)
```typescript
interface StatusData {
  status: 'To Do' | 'In Progress' | 'In Review' | 'Done';
  count: number;
  percentage: number;
}
```

### 3. Team Workload (Bar Chart)
```typescript
interface WorkloadData {
  userId: number;
  userName: string;
  assignedTasks: number;
  completedTasks: number;
  inProgressTasks: number;
}
```

### 4. Completion Trend (Line Chart)
```typescript
interface TrendData {
  date: string;
  completed: number;
  created: number;
  target: number;        // Optional target line
}
```

### 5. Priority Breakdown (Doughnut Chart)
```typescript
interface PriorityData {
  priority: 'Low' | 'Medium' | 'High' | 'Critical';
  count: number;
  color: string;
}
```

## 🔧 API Integration

### Fetching Analytics Data

```typescript
// src/services/analyticsService.ts

import axios from 'axios';

const API_URL = process.env.REACT_APP_API_URL;

export const analyticsService = {
  // Get dashboard overview
  getDashboardMetrics: async (dateRange: DateRange) => {
    const response = await axios.get(`${API_URL}/analytics/dashboard`, {
      params: {
        startDate: dateRange.start,
        endDate: dateRange.end
      },
      headers: {
        Authorization: `Bearer ${getToken()}`
      }
    });
    return response.data;
  },

  // Get team performance data
  getTeamPerformance: async (teamId?: number) => {
    const response = await axios.get(`${API_URL}/analytics/team`, {
      params: { teamId }
    });
    return response.data;
  },

  // Get task statistics
  getTaskStats: async (filters: TaskFilters) => {
    const response = await axios.get(`${API_URL}/analytics/tasks/stats`, {
      params: filters
    });
    return response.data;
  },

  // Export report
  exportReport: async (format: 'pdf' | 'excel', data: ReportData) => {
    const response = await axios.post(
      `${API_URL}/analytics/export/${format}`,
      data,
      { responseType: 'blob' }
    );
    return response.data;
  }
};
```

### Using React Query

```typescript
// src/hooks/useAnalytics.ts

import { useQuery } from '@tanstack/react-query';
import { analyticsService } from '../services/analyticsService';

export const useAnalytics = (dateRange: DateRange) => {
  return useQuery({
    queryKey: ['analytics', 'dashboard', dateRange],
    queryFn: () => analyticsService.getDashboardMetrics(dateRange),
    refetchInterval: 30000, // Refetch every 30 seconds
    staleTime: 10000
  });
};

export const useTeamPerformance = (teamId?: number) => {
  return useQuery({
    queryKey: ['analytics', 'team', teamId],
    queryFn: () => analyticsService.getTeamPerformance(teamId)
  });
};
```

## 🎨 Components

### Dashboard Layout

```typescript
// src/components/Dashboard.tsx

import React, { useState } from 'react';
import { Grid, Paper } from '@mui/material';
import MetricCard from './MetricCard';
import TaskStatusChart from './TaskStatusChart';
import TeamWorkloadChart from './TeamWorkloadChart';
import CompletionTrendChart from './CompletionTrendChart';
import { useAnalytics } from '../hooks/useAnalytics';

const Dashboard: React.FC = () => {
  const [dateRange, setDateRange] = useState(getLast30Days());
  const { data, isLoading } = useAnalytics(dateRange);

  if (isLoading) return <LoadingSpinner />;

  return (
    <Grid container spacing={3}>
      {/* Metric Cards */}
      <Grid item xs={12} sm={6} md={3}>
        <MetricCard
          title="Total Tasks"
          value={data.totalTasks}
          change={data.taskChange}
          trend={data.taskTrend}
        />
      </Grid>
      
      {/* Charts */}
      <Grid item xs={12} md={6}>
        <Paper>
          <TaskStatusChart data={data.statusDistribution} />
        </Paper>
      </Grid>
      
      <Grid item xs={12} md={6}>
        <Paper>
          <TeamWorkloadChart data={data.teamWorkload} />
        </Paper>
      </Grid>
      
      <Grid item xs={12}>
        <Paper>
          <CompletionTrendChart data={data.completionTrend} />
        </Paper>
      </Grid>
    </Grid>
  );
};
```

### Chart Components

```typescript
// src/components/TaskStatusChart.tsx

import React from 'react';
import { Pie } from 'react-chartjs-2';
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js';

ChartJS.register(ArcElement, Tooltip, Legend);

interface Props {
  data: StatusData[];
}

const TaskStatusChart: React.FC<Props> = ({ data }) => {
  const chartData = {
    labels: data.map(item => item.status),
    datasets: [{
      data: data.map(item => item.count),
      backgroundColor: [
        '#3498db', // To Do - Blue
        '#f39c12', // In Progress - Orange
        '#9b59b6', // In Review - Purple
        '#2ecc71', // Done - Green
      ],
      borderWidth: 2,
    }]
  };

  const options = {
    responsive: true,
    plugins: {
      legend: {
        position: 'bottom' as const,
      },
      tooltip: {
        callbacks: {
          label: (context: any) => {
            const label = context.label || '';
            const value = context.parsed || 0;
            const percentage = data[context.dataIndex].percentage;
            return `${label}: ${value} (${percentage}%)`;
          }
        }
      }
    }
  };

  return (
    <div style={{ padding: '20px' }}>
      <h3>Task Status Distribution</h3>
      <Pie data={chartData} options={options} />
    </div>
  );
};
```

## 📥 Export Functionality

### PDF Export

```typescript
// src/utils/exportToPDF.ts

import jsPDF from 'jspdf';
import html2canvas from 'html2canvas';

export const exportDashboardToPDF = async () => {
  const dashboard = document.getElementById('dashboard-container');
  
  if (!dashboard) return;

  const canvas = await html2canvas(dashboard, {
    scale: 2,
    logging: false
  });

  const imgData = canvas.toDataURL('image/png');
  const pdf = new jsPDF({
    orientation: 'landscape',
    unit: 'px',
    format: [canvas.width, canvas.height]
  });

  pdf.addImage(imgData, 'PNG', 0, 0, canvas.width, canvas.height);
  pdf.save(`analytics-report-${new Date().toISOString()}.pdf`);
};
```

### Excel Export

```typescript
// src/utils/exportToExcel.ts

import * as XLSX from 'xlsx';

export const exportToExcel = (data: any[], filename: string) => {
  const worksheet = XLSX.utils.json_to_sheet(data);
  const workbook = XLSX.utils.book_new();
  
  XLSX.utils.book_append_sheet(workbook, worksheet, 'Analytics');
  XLSX.writeFile(workbook, `${filename}.xlsx`);
};

// Usage
exportToExcel(analyticsData, 'task-analytics-2024');
```

## 🔄 Real-time Updates

```typescript
// src/hooks/useWebSocket.ts

import { useEffect, useState } from 'react';
import io from 'socket.io-client';

export const useWebSocket = () => {
  const [socket, setSocket] = useState<any>(null);

  useEffect(() => {
    const newSocket = io(process.env.REACT_APP_WS_URL!, {
      auth: {
        token: localStorage.getItem('token')
      }
    });

    newSocket.on('analytics-update', (data) => {
      // Update analytics data in real-time
      queryClient.invalidateQueries(['analytics']);
    });

    setSocket(newSocket);

    return () => newSocket.close();
  }, []);

  return socket;
};
```

## 🧪 Testing

```bash
# Run tests
npm test

# Run tests with coverage
npm test -- --coverage

# Run tests in watch mode
npm test -- --watch
```

### Example Test

```typescript
// src/components/__tests__/Dashboard.test.tsx

import { render, screen } from '@testing-library/react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import Dashboard from '../Dashboard';

const queryClient = new QueryClient();

describe('Dashboard', () => {
  it('renders metric cards', async () => {
    render(
      <QueryClientProvider client={queryClient}>
        <Dashboard />
      </QueryClientProvider>
    );

    expect(await screen.findByText('Total Tasks')).toBeInTheDocument();
    expect(await screen.findByText('Completed Tasks')).toBeInTheDocument();
  });

  it('displays charts', async () => {
    render(
      <QueryClientProvider client={queryClient}>
        <Dashboard />
      </QueryClientProvider>
    );

    expect(await screen.findByText('Task Status Distribution')).toBeInTheDocument();
  });
});
```

## 📁 Project Structure

```
analytics-dashboard/
├── public/
├── src/
│   ├── components/
│   │   ├── Dashboard.tsx
│   │   ├── MetricCard.tsx
│   │   ├── TaskStatusChart.tsx
│   │   ├── TeamWorkloadChart.tsx
│   │   ├── CompletionTrendChart.tsx
│   │   └── ExportMenu.tsx
│   ├── hooks/
│   │   ├── useAnalytics.ts
│   │   ├── useWebSocket.ts
│   │   └── useExport.ts
│   ├── services/
│   │   └── analyticsService.ts
│   ├── utils/
│   │   ├── exportToPDF.ts
│   │   ├── exportToExcel.ts
│   │   └── dateHelpers.ts
│   ├── types/
│   │   └── analytics.ts
│   ├── App.tsx
│   └── index.tsx
├── package.json
├── tsconfig.json
└── README.md
```

## 🚀 Deployment

### Build for Production

```bash
npm run build
# or
yarn build
```

### Deploy to Netlify

```bash
# Install Netlify CLI
npm install -g netlify-cli

# Deploy
netlify deploy --prod --dir=build
```

### Deploy to Vercel

```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel --prod
```

## 🔧 Performance Optimization

- **Code Splitting**: Routes lazy-loaded with React.lazy()
- **Memoization**: useMemo and useCallback for expensive operations
- **Virtual Scrolling**: For large data tables
- **Debounced Filtering**: Reduces API calls during user input
- **Chart Optimization**: Canvas-based rendering for large datasets

## 📈 Future Enhancements

- [ ] Predictive analytics using ML
- [ ] Custom widget builder
- [ ] Scheduled report delivery via email
- [ ] Multi-team comparison views
- [ ] Advanced filtering and grouping
- [ ] Dark mode theme
- [ ] Mobile app version
- [ ] Integration with BI tools (Tableau, Power BI)

## 🐛 Known Issues

- Large datasets (>10,000 tasks) may slow chart rendering
- PDF export doesn't work well on mobile browsers
- Real-time updates require stable WebSocket connection

## 📝 License

MIT License - see [LICENSE](LICENSE) file

## 👨‍💻 Author

**[Your Name]**
- GitHub: [@your-username](https://github.com/your-username)
- LinkedIn: [Your Profile](https://linkedin.com/in/your-profile)
- Email: your.email@example.com

## 🙏 Acknowledgments

Built as part of the Task Management System project at AMDOX.

Special thanks to:
- Chart.js team for excellent visualization library
- Material-UI for component framework
- React Query team for data fetching solution

---

⭐ If you find this dashboard useful, please star the repository!
