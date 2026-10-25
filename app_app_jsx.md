import React, { useState, useEffect, useMemo } from 'react';
import {
  Search,
  Clock,
  MapPin,
  Star,
  Settings,
  Snowflake,
  Accessibility,
  Usb,
  Bike,
  Ticket,
  RefreshCw,
  ChevronRight,
  ChevronDown,
  X,
  Moon,
  Sun,
  Check,
  ArrowLeft,
  Plus,
  Minus,
  Radio,
  Bus,
  TrainFront,
  Palette,
  Database,
  List
} from 'lucide-react';

// --- BAZA DANYCH WSZYSTKICH PRZYSTANKÓW W POZNANIU (GTFS ZTM) ---
const STOPS_DATA = [
  {
    id: 'RKAP01',
    name: 'Rondo Kaponiera',
    postNumber: '01',
    distanceText: '120 m · najbliżej',
    mainLineBadge: '14',
    zone: 'Strefa A',
    code: 'RKAP01',
    location: 'Jeżyce / Centrum (poziom 0, kier. PST / Jeżyce)',
    lines: ['2', '8', '10', '14', '18', '169', '175'],
    departures: [
      {
        id: 'dep-1',
        line: '14',
        type: 'tram',
        direction: 'Os. Sobieskiego',
        brigade: 'brygada 3',
        brigadeNumber: '3',
        vehicleId: '2043',
        vehicleModel: 'Moderus Gamma MF 02 AC',
        minutes: 0,
        displayTime: '<1',
        status: 'realtime-now',
        delayText: 'opóźnienie + 1 min',
        livePillText: 'do Ciebie < 1 min · opóźnienie + 1 min',
        features: ['ac', 'lowfloor', 'usb', 'bike', 'ticket'],
        userStopIndex: 3,
        vehiclePosition: 3.0,
        route: [
          { name: 'Górczyn PKM', subtitle: '3 przystanki przed Tobą', clock: '12:34', etaLabel: 'odjechał' },
          { name: 'Rynek Łazarski', subtitle: '2 przystanki przed Tobą', clock: '12:36', etaLabel: 'odjechał' },
          { name: 'Dworzec Zachodni', subtitle: '1 przystanek przed Tobą', clock: '12:38', etaLabel: 'odjechał' },
          { name: 'Rondo Kaponiera', subtitle: 'Twój przystanek', clock: '12:40', etaLabel: 'teraz' },
          { name: 'Most Teatralny', subtitle: '1 przystanek dalej', clock: '12:42', etaLabel: 'za ~ 3 min' },
          { name: 'Słowiańska', subtitle: '2 przystanki dalej', clock: '12:44', etaLabel: 'za ~ 5 min' },
          { name: 'Aleje Solidarności', subtitle: '3 przystanki dalej', clock: '12:46', etaLabel: 'za ~ 7 min' },
          { name: 'Os. Sobieskiego', subtitle: '4 przystanki dalej · pętla', clock: '12:48', etaLabel: 'za ~ 9 min' }
        ]
      },
      {
        id: 'dep-2',
        line: '18',
        type: 'tram',
        direction: 'Franowo',
        brigade: 'brygada 7',
        brigadeNumber: '7',
        vehicleId: '417',
        vehicleModel: 'Solaris Tramino S105p',
        minutes: 3,
        displayTime: '3',
        status: 'realtime-soon',
        delayText: 'opóźnienie + 1 min',
        livePillText: 'do Ciebie 3 min · opóźnienie + 1 min',
        features: ['ac', 'lowfloor', 'ticket'],
        userStopIndex: 3,
        vehiclePosition: 1.65,
        route: [
          { name: 'Ogrody', subtitle: '3 przystanki przed Tobą', clock: '12:36', etaLabel: 'odjechał' },
          { name: 'Żeromskiego', subtitle: '2 przystanki przed Tobą', clock: '12:38', etaLabel: 'odjechał' },
          { name: 'Rynek Jeżycki', subtitle: '1 przystanek przed Tobą', clock: '12:41', etaLabel: 'za 1 min' },
          { name: 'Rondo Kaponiera', subtitle: 'Twój przystanek · odjazd za 3 min', clock: '12:43', etaLabel: 'za 3 min' },
          { name: 'Most Dworcowy', subtitle: '1 przystanek dalej', clock: '12:46', etaLabel: 'za ~ 6 min' },
          { name: 'Półwiejska', subtitle: '2 przystanki dalej', clock: '12:49', etaLabel: 'za ~ 9 min' },
          { name: 'Rondo Rataje', subtitle: '3 przystanki dalej', clock: '12:53', etaLabel: 'za ~ 13 min' },
          { name: 'Franowo', subtitle: '4 przystanki dalej · pętla', clock: '13:02', etaLabel: 'za ~ 22 min' }
        ]
      },
      {
        id: 'dep-3',
        line: '8',
        type: 'tram',
        direction: 'Ogrody',
        brigade: 'brygada 5',
        brigadeNumber: '5',
        vehicleId: '',
        vehicleModel: '',
        minutes: 6,
        displayTime: '6',
        status: 'scheduled',
        delayText: 'czas rozkładowy (brak połączenia GPS)',
        livePillText: 'do Ciebie 6 min · kurs rozkładowy (offline)',
        features: [],
        userStopIndex: 3,
        vehiclePosition: 1.0,
        route: [
          { name: 'Miłostowo', subtitle: '3 przystanki przed Tobą (wg rozkładu)', clock: '12:28', etaLabel: 'rozkładowo' },
          { name: 'Rondo Śródka', subtitle: '2 przystanki przed Tobą (wg rozkładu)', clock: '12:36', etaLabel: 'rozkładowo' },
          { name: 'Most Teatralny', subtitle: '1 przystanek przed Tobą (wg rozkładu)', clock: '12:43', etaLabel: 'za ~ 3 min' },
          { name: 'Rondo Kaponiera', subtitle: 'Twój przystanek · orientacyjnie wg rozkładu', clock: '12:46', etaLabel: 'za 6 min' },
          { name: 'Rynek Jeżycki', subtitle: '1 przystanek dalej', clock: '12:49', etaLabel: 'za ~ 9 min' },
          { name: 'Żeromskiego', subtitle: '2 przystanki dalej', clock: '12:53', etaLabel: 'za ~ 13 min' },
          { name: 'Polska', subtitle: '3 przystanki dalej', clock: '12:55', etaLabel: 'za ~ 15 min' },
          { name: 'Ogrody', subtitle: '4 przystanki dalej · pętla', clock: '12:57', etaLabel: 'za ~ 17 min' }
        ]
      },
      {
        id: 'dep-4',
        line: '175',
        type: 'bus',
        direction: 'Dębiec',
        brigade: 'brygada 12',
        brigadeNumber: '12',
        vehicleId: '4821',
        vehicleModel: 'Solaris Urbino 18 IV Electric',
        minutes: 10,
        displayTime: '10',
        status: 'realtime-later',
        delayText: 'punktualnie (GPS)',
        livePillText: 'do Ciebie 10 min · punktualnie',
        features: ['ac', 'lowfloor', 'usb', 'ticket'],
        userStopIndex: 3,
        vehiclePosition: 0.75,
        route: [
          { name: 'Os. Sobieskiego', subtitle: '3 przystanki przed Tobą', clock: '12:38', etaLabel: 'odjechał' },
          { name: 'Słowiańska', subtitle: '2 przystanki przed Tobą', clock: '12:42', etaLabel: 'za 2 min' },
          { name: 'Most Teatralny', subtitle: '1 przystanek przed Tobą', clock: '12:47', etaLabel: 'za 7 min' },
          { name: 'Rondo Kaponiera', subtitle: 'Twój przystanek · odjazd za 10 min', clock: '12:50', etaLabel: 'za 10 min' },
          { name: 'Bałtyk', subtitle: '1 przystanek dalej', clock: '12:52', etaLabel: 'za ~ 12 min' },
          { name: 'Poznań Główny', subtitle: '2 przystanki dalej', clock: '12:56', etaLabel: 'za ~ 16 min' },
          { name: 'Wspólna', subtitle: '3 przystanki dalej', clock: '13:04', etaLabel: 'za ~ 24 min' },
          { name: 'Dębiec PKM', subtitle: '4 przystanki dalej · pętla', clock: '13:09', etaLabel: 'za ~ 29 min' }
        ]
      }
    ]
  },
  {
    id: 'MTEA01',
    name: 'Most Teatralny',
    postNumber: '01',
    distanceText: '340 m',
    mainLineBadge: '175',
    zone: 'Strefa A',
    code: 'MTEA01',
    location: 'Centrum / Roosevelta (kier. Górczyn PKM)',
    lines: ['175', '169', '8', '14'],
    departures: [
      {
        id: 'dep-mt01-1',
        line: '175',
        type: 'bus',
        direction: 'Górczyn PKM',
        brigade: 'brygada 12',
        brigadeNumber: '12',
        vehicleId: '4821',
        vehicleModel: 'Solaris Urbino 18 IV Electric',
        minutes: 4,
        displayTime: '4',
        status: 'realtime-later',
        delayText: 'punktualnie (GPS)',
        livePillText: 'do Ciebie 4 min · punktualnie',
        features: ['ac', 'lowfloor', 'usb', 'ticket'],
        userStopIndex: 3,
        vehiclePosition: 1.5,
        route: [
          { name: 'Os. Sobieskiego', subtitle: '3 przystanki przed Tobą', clock: '10:42', etaLabel: 'odjechał' },
          { name: 'Kurpińskiego', subtitle: '2 przystanki przed Tobą', clock: '10:46', etaLabel: 'odjechał' },
          { name: 'Słowiańska', subtitle: '1 przystanek przed Tobą', clock: '10:51', etaLabel: 'za 1 min' },
          { name: 'Most Teatralny', subtitle: 'Twój przystanek · odjazd za 4 min', clock: '10:54', etaLabel: 'za 4 min' },
          { name: 'Rondo Kaponiera', subtitle: '1 przystanek dalej', clock: '10:57', etaLabel: 'za ~ 7 min' },
          { name: 'Bałtyk', subtitle: '2 przystanki dalej', clock: '10:59', etaLabel: 'za ~ 9 min' },
          { name: 'Głogowska/Hetmańska', subtitle: '3 przystanki dalej', clock: '11:08', etaLabel: 'za ~ 18 min' },
          { name: 'Górczyn PKM', subtitle: '4 przystanki dalej · pętla', clock: '11:16', etaLabel: 'za ~ 26 min' }
        ]
      }
    ]
  },
  {
    id: 'RRAT01',
    name: 'Rondo Rataje',
    postNumber: '02',
    distanceText: '1.4 km',
    mainLineBadge: '18',
    zone: 'Strefa A',
    code: 'RRAT02',
    location: 'Rataje / Zamenhofa (węzeł przesiadkowy)',
    lines: ['5', '6', '12', '18', '174', '184'],
    departures: [
      {
        id: 'dep-rrat-1',
        line: '18',
        type: 'tram',
        direction: 'Ogrody',
        brigade: 'brygada 2',
        brigadeNumber: '2',
        vehicleId: '422',
        vehicleModel: 'Solaris Tramino S105p',
        minutes: 2,
        displayTime: '2',
        status: 'realtime-soon',
        delayText: 'punktualnie (GPS)',
        livePillText: 'do Ciebie 2 min · punktualnie',
        features: ['ac', 'lowfloor', 'ticket'],
        userStopIndex: 2,
        vehiclePosition: 2.0,
        route: [
          { name: 'Franowo', subtitle: '2 przystanki przed Tobą', clock: '12:30', etaLabel: 'odjechał' },
          { name: 'Kórnicka', subtitle: '1 przystanek przed Tobą', clock: '12:35', etaLabel: 'odjechał' },
          { name: 'Rondo Rataje', subtitle: 'Twój przystanek', clock: '12:38', etaLabel: 'teraz' },
          { name: 'Stary Browar', subtitle: '1 przystanek dalej', clock: '12:41', etaLabel: 'za 3 min' },
          { name: 'Ogrody', subtitle: '2 przystanki dalej · pętla', clock: '12:55', etaLabel: 'za ~ 17 min' }
        ]
      }
    ]
  },
  {
    id: 'PGLO01',
    name: 'Poznań Główny',
    postNumber: '03',
    distanceText: '850 m',
    mainLineBadge: '12',
    zone: 'Strefa A',
    code: 'PGLO03',
    location: 'Dworzec Zachodni / PST / Matyi',
    lines: ['6', '10', '12', '14', '18', '159', '175'],
    departures: [
      {
        id: 'dep-pglo-1',
        line: '12',
        type: 'tram',
        direction: 'Starołęka PKM',
        brigade: 'brygada 4',
        brigadeNumber: '4',
        vehicleId: '512',
        vehicleModel: 'Moderus Beta MF 20 AC',
        minutes: 5,
        displayTime: '5',
        status: 'realtime-soon',
        delayText: 'opóźnienie + 2 min',
        livePillText: 'do Ciebie 5 min · opóźnienie + 2 min',
        features: ['ac', 'lowfloor', 'usb'],
        userStopIndex: 1,
        vehiclePosition: 1.0,
        route: [
          { name: 'Jeżyce', subtitle: '1 przystanek przed Tobą', clock: '12:40', etaLabel: 'odjechał' },
          { name: 'Poznań Główny', subtitle: 'Twój przystanek', clock: '12:45', etaLabel: 'za 5 min' },
          { name: 'Starołęka PKM', subtitle: '2 przystanki dalej · pętla', clock: '13:02', etaLabel: 'za ~ 22 min' }
        ]
      }
    ]
  },
  {
    id: 'BALT01',
    name: 'Bałtyk',
    postNumber: '01',
    distanceText: '300 m',
    mainLineBadge: '18',
    zone: 'Strefa A',
    code: 'BALT01',
    location: 'Roosevelta / Zwierzyniecka',
    lines: ['2', '8', '11', '14', '18', '169'],
    departures: []
  },
  {
    id: 'OSOB01',
    name: 'Os. Sobieskiego',
    postNumber: 'Pętla',
    distanceText: '6.2 km',
    mainLineBadge: '14',
    zone: 'Strefa A',
    code: 'OSOB01',
    location: 'Pętla PST Piątkowo',
    lines: ['12', '14', '15', '16', '175', '185', '191'],
    departures: []
  },
  {
    id: 'GORC01',
    name: 'Górczyn PKM',
    postNumber: 'Pętla',
    distanceText: '5.1 km',
    mainLineBadge: '5',
    zone: 'Strefa A',
    code: 'GORC01',
    location: 'Zintegrowany Węzeł Przesiadkowy Górczyn',
    lines: ['5', '8', '14', '175', '180'],
    departures: []
  },
  {
    id: 'DEBI01',
    name: 'Dębiec PKM',
    postNumber: 'Pętla',
    distanceText: '4.8 km',
    mainLineBadge: '2',
    zone: 'Strefa A',
    code: 'DEBI01',
    location: 'Pętla tramwajowo-autobusowa Dębiec',
    lines: ['2', '9', '10', '175', '171'],
    departures: []
  },
  {
    id: 'SROD01',
    name: 'Rondo Śródka',
    postNumber: '01',
    distanceText: '2.5 km',
    mainLineBadge: '6',
    zone: 'Strefa A',
    code: 'SROD01',
    location: 'Węzeł komunikacyjny Śródka',
    lines: ['6', '8', '16', '173', '185', '312'],
    departures: []
  }
];

// Kształt 6-Sided Cookie (Material Design 3 Expressive "Ciastko") dla pojazdów na naszym przystanku
const M3SixSidedCookie = ({ color, children, size = 34, className = '' }) => {
  const points = [];
  const cx = 50;
  const cy = 50;
  const baseR = 40;
  const amp = 7.5;
  const steps = 72;
  for (let i = 0; i < steps; i++) {
    const theta = (i / steps) * Math.PI * 2;
    const r = baseR + amp * Math.cos(6 * theta);
    const x = (cx + r * Math.cos(theta)).toFixed(2);
    const y = (cy + r * Math.sin(theta)).toFixed(2);
    points.push(`${i === 0 ? 'M' : 'L'} ${x} ${y}`);
  }
  const pathData = points.join(' ') + ' Z';

  return (
    <div
      style={{ width: size, height: size }}
      className={`relative inline-flex items-center justify-center shrink-0 select-none ${className}`}
    >
      <svg viewBox="0 0 100 100" className="w-full h-full drop-shadow-xs">
        <path d={pathData} fill={color} />
      </svg>
      <div className="absolute inset-0 flex items-center justify-center text-white">
        {children}
      </div>
    </div>
  );
};

// Standardowy kształt ikony pojazdu w drodze (squircle)
const StandardVehicleMarker = ({ color, children, size = 28, className = '' }) => {
  return (
    <div
      style={{ width: size, height: size, backgroundColor: color }}
      className={`relative inline-flex items-center justify-center rounded-[9px] text-white shadow-xs shrink-0 select-none ${className}`}
    >
      {children}
    </div>
  );
};

// Wolniejsza fala Material Design 3 Expressive o płynnym przepływie (4.2s)
const M3VerticalLiveWave = ({ color, progressPercent = 100 }) => {
  if (progressPercent <= 0) return null;
  return (
    <div
      style={{ height: `calc(${progressPercent}% + 6px)` }}
      className="absolute left-[7px] top-3 w-[14px] overflow-hidden pointer-events-none z-0 transition-all duration-500"
    >
      <svg
        viewBox="0 0 14 120"
        preserveAspectRatio="none"
        className="w-full h-[200%] animate-m3-vertical-wave"
      >
        <path
          d="M 7 0 Q 13 5, 7 10 T 7 20 T 7 30 T 7 40 T 7 50 T 7 60 T 7 70 T 7 80 T 7 90 T 7 100 T 7 110 T 7 120"
          fill="none"
          stroke={color}
          strokeWidth="2.8"
          strokeLinecap="round"
        />
      </svg>
    </div>
  );
};

// Oficjalne kolory linii MPK Poznań
const getLineBadgeStyle = (line) => {
  switch (line) {
    case '14':
      return { bg: '#7D5227', text: '#FFFFFF' };
    case '18':
    case '6':
      return { bg: '#F2C511', text: '#111827' };
    case '8':
    case '12':
      return { bg: '#D13B3B', text: '#FFFFFF' };
    case '175':
    case '159':
    case '184':
      return { bg: '#6E7278', text: '#FFFFFF' };
    case '10':
    case '16':
      return { bg: '#2B6CB0', text: '#FFFFFF' };
    case '15':
      return { bg: '#2F855A', text: '#FFFFFF' };
    default:
      return { bg: '#4A5568', text: '#FFFFFF' };
  }
};

export default function App() {
  const [activeTab, setActiveTab] = useState('odjazdy'); // 'odjazdy' | 'przystanki' | 'ulubione' | 'ustawienia'
  const [selectedStopId, setSelectedStopId] = useState('RKAP01');
  const [expandedStopId, setExpandedStopId] = useState(null); // do zwijania/rozwijania przystanków w zakładce Przystanki
  const [searchQuery, setSearchQuery] = useState('');
  const [favorites, setFavorites] = useState(['RKAP01', 'MTEA01']);
  const [selectedDeparture, setSelectedDeparture] = useState(null);
  const [liveVehiclePos, setLiveVehiclePos] = useState(null);
  const [isRefreshing, setIsRefreshing] = useState(false);
  const [lastUpdated, setLastUpdated] = useState(new Date());
  const [darkMode, setDarkMode] = useState(false);
  const [autoRefreshSec] = useState(15);
  const [showStopSelectorModal, setShowStopSelectorModal] = useState(false);
  const [toastMessage, setToastMessage] = useState(null);
  const [mapZoom, setMapZoom] = useState(1);
  const [settingsSubModal, setSettingsSubModal] = useState(null);
  const [bumpAnimKey, setBumpAnimKey] = useState(0);

  const [stopsState] = useState(STOPS_DATA);

  const currentStop = useMemo(() => {
    return stopsState.find((s) => s.id === selectedStopId) || stopsState[0];
  }, [stopsState, selectedStopId]);

  useEffect(() => {
    if (selectedDeparture) {
      setLiveVehiclePos(
        typeof selectedDeparture.vehiclePosition === 'number'
          ? selectedDeparture.vehiclePosition
          : selectedDeparture.userStopIndex ?? 3
      );
    } else {
      setLiveVehiclePos(null);
    }
  }, [selectedDeparture]);

  const showToast = (msg) => {
    setToastMessage(msg);
    setTimeout(() => {
      setToastMessage(null);
    }, 2600);
  };

  const handleRefresh = () => {
    setIsRefreshing(true);
    setTimeout(() => {
      setLastUpdated(new Date());
      if (selectedDeparture && !isDepartureOffline(selectedDeparture)) {
        setLiveVehiclePos((prev) => {
          const maxIdx = selectedDeparture.route.length - 1;
          const next = Math.min(maxIdx, Number(((prev ?? 0) + 0.25).toFixed(2)));
          return next;
        });
      }
      setIsRefreshing(false);
      showToast('Zaktualizowano pozycje GPS pojazdów ZTM Poznań');
    }, 550);
  };

  useEffect(() => {
    if (!autoRefreshSec) return;
    const interval = setInterval(() => {
      setLastUpdated(new Date());
    }, autoRefreshSec * 1000);
    return () => clearInterval(interval);
  }, [autoRefreshSec]);

  const toggleFavoriteStop = (stopId) => {
    setFavorites((prev) => {
      const exists = prev.includes(stopId);
      const updated = exists ? prev.filter((id) => id !== stopId) : [...prev, stopId];
      showToast(exists ? 'Usunięto przystanek z ulubionych' : 'Dodano przystanek do ulubionych');
      return updated;
    });
  };

  const isDepartureOffline = (dep) => {
    return !dep || dep.status === 'scheduled' || !dep.vehicleId;
  };

  const filteredDepartures = useMemo(() => {
    const q = searchQuery.trim().toLowerCase();
    return currentStop.departures.filter((dep) => {
      if (!q) return true;
      return (
        dep.line.toLowerCase().includes(q) ||
        dep.direction.toLowerCase().includes(q) ||
        dep.vehicleId.toLowerCase().includes(q) ||
        dep.brigade.toLowerCase().includes(q) ||
        currentStop.name.toLowerCase().includes(q) ||
        currentStop.code.toLowerCase().includes(q)
      );
    });
  }, [currentStop, searchQuery]);

  const matchingOtherStops = useMemo(() => {
    const q = searchQuery.trim().toLowerCase();
    if (q.length < 2) return [];
    return stopsState.filter(
      (s) =>
        s.id !== currentStop.id &&
        (s.name.toLowerCase().includes(q) ||
          s.code.toLowerCase().includes(q) ||
          s.lines.some((l) => l.toLowerCase() === q))
    );
  }, [stopsState, currentStop, searchQuery]);

  // Wyposażenie z przezroczystym tłem w kolorze danej linii
  const renderFeatureIconsMain = (features, isOffline = false, lineColorHex = '#6E7278') => {
    if (isOffline || !features || features.length === 0) return null;
    return (
      <div
        style={{ backgroundColor: `${lineColorHex}18` }}
        className="inline-flex items-center gap-1.5 px-2 py-0.5 rounded-md mr-1.5 backdrop-blur-2xs"
      >
        {features.includes('ac') && <Snowflake className="w-3.5 h-3.5 stroke-[2.2]" style={{ color: lineColorHex }} title="Klimatyzacja" />}
        {features.includes('lowfloor') && <Accessibility className="w-3.5 h-3.5 stroke-[2.2]" style={{ color: lineColorHex }} title="Niska podłoga" />}
        {features.includes('usb') && <Usb className="w-3.5 h-3.5 stroke-[2.2]" style={{ color: lineColorHex }} title="Ładowarka USB" />}
        {features.includes('bike') && <Bike className="w-3.5 h-3.5 stroke-[2.2]" style={{ color: lineColorHex }} title="Rower" />}
        {features.includes('ticket') && <Ticket className="w-3.5 h-3.5 stroke-[2.2]" style={{ color: lineColorHex }} title="Biletomat" />}
      </div>
    );
  };

  const renderStatusDot = (status) => {
    switch (status) {
      case 'realtime-now':
        return <span title="GPS na żywo (<1 min)" className="w-2.5 h-2.5 rounded-full bg-[#19A957] inline-block mr-1.5 animate-soft-blink" />;
      case 'realtime-soon':
        return <span title="GPS na żywo" className="w-2.5 h-2.5 rounded-full bg-[#E5A000] inline-block mr-1.5 animate-soft-pulse" />;
      case 'realtime-orange':
        return <span title="GPS na żywo" className="w-2.5 h-2.5 rounded-full bg-[#F06418] inline-block mr-1.5 animate-soft-pulse" />;
      case 'realtime-later':
        return <span title="GPS na żywo" className="w-2.5 h-2.5 rounded-full bg-[#7D5227] inline-block mr-1.5 animate-soft-pulse" />;
      case 'scheduled':
      default:
        return (
          <span
            title="Rozkładowy (offline)"
            className={`w-2.5 h-2.5 rounded-full border-[1.8px] inline-block mr-1.5 animate-soft-pulse ${
              darkMode ? 'border-slate-300' : 'border-[#1E242B]'
            }`}
          />
        );
    }
  };

  const selectedIsOffline = isDepartureOffline(selectedDeparture);
  const currentPos =
    liveVehiclePos !== null
      ? liveVehiclePos
      : selectedDeparture?.vehiclePosition ?? selectedDeparture?.userStopIndex ?? 3;
  const userStopIdx = selectedDeparture?.userStopIndex ?? 3;

  return (
    <div
      className={`min-h-screen w-full flex flex-col items-center justify-center sm:py-6 sm:px-4 font-sans transition-colors duration-200 ${
        darkMode ? 'bg-[#0B0F17] text-slate-100' : 'bg-[#E2E8F0] text-slate-900'
      }`}
    >
      <style>{`
        @keyframes busBump {
          0% { transform: translateX(0) rotate(0deg); }
          30% { transform: translateX(10px) rotate(0deg); }
          42% { transform: translateX(12px) rotate(13deg); }
          58% { transform: translateX(-5px) rotate(-6deg); }
          75% { transform: translateX(2px) rotate(2deg); }
          100% { transform: translateX(0) rotate(0deg); }
        }
        @keyframes tramBump {
          0% { transform: translateX(0) rotate(0deg); }
          30% { transform: translateX(-10px) rotate(0deg); }
          42% { transform: translateX(-12px) rotate(-13deg); }
          58% { transform: translateX(5px) rotate(6deg); }
          75% { transform: translateX(-2px) rotate(-2deg); }
          100% { transform: translateX(0) rotate(0deg); }
        }
        .animate-bus-bump {
          transform-origin: top right;
          animation: busBump 1.35s cubic-bezier(0.22, 1, 0.36, 1) infinite;
          animation-delay: 0.4s;
        }
        .animate-tram-bump {
          transform-origin: top left;
          animation: tramBump 1.35s cubic-bezier(0.22, 1, 0.36, 1) infinite;
          animation-delay: 0.4s;
        }

        /* Wolniejszy, płynny przepływ pionowej fali M3 Expressive (4.2s) */
        @keyframes m3VerticalWaveFlow {
          0% { transform: translateY(-50%); }
          100% { transform: translateY(0%); }
        }
        .animate-m3-vertical-wave {
          animation: m3VerticalWaveFlow 4.2s linear infinite;
        }

        /* Delikatne, spokojne mruganie dla pojazdów podjeżdżających (<1 min) */
        @keyframes softBlink {
          0% { opacity: 1; transform: scale(1); }
          50% { opacity: 0.35; transform: scale(0.92); }
          100% { opacity: 1; transform: scale(1); }
        }
        .animate-soft-blink {
          animation: softBlink 2.4s ease-in-out infinite;
        }

        @keyframes softPulse {
          0% { transform: scale(1); opacity: 1; }
          50% { transform: scale(1.15); opacity: 0.8; }
          100% { transform: scale(1); opacity: 1; }
        }
        .animate-soft-pulse {
          animation: softPulse 2.2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        }
      `}</style>

      {/* GŁÓWNA RAMKA URZĄDZENIA / TELEFONU */}
      <div
        className={`w-full max-w-[412px] h-screen sm:h-[860px] sm:rounded-[34px] flex flex-col justify-between overflow-hidden shadow-2xl border transition-colors relative ${
          darkMode ? 'bg-[#12161F] border-slate-800 text-slate-100' : 'bg-[#FAFBFD] border-[#CBD5E1] text-[#111827]'
        }`}
      >
        {/* Toast powiadomienie */}
        {toastMessage && (
          <div className="absolute top-16 left-1/2 -translate-x-1/2 z-50 bg-slate-900/95 text-white text-xs font-medium px-4 py-2.5 rounded-full shadow-lg flex items-center gap-2 whitespace-nowrap animate-fade-in">
            <Check className="w-3.5 h-3.5 text-emerald-400" />
            {toastMessage}
          </div>
        )}

        {/* GÓRNY PASEK AKCJI / WYSZUKIWANIA */}
        {activeTab === 'ustawienia' ? null : selectedDeparture && activeTab === 'odjazdy' ? (
          <div className="px-3.5 pt-3.5 pb-2 shrink-0 flex items-center justify-between">
            <button
              onClick={() => setSelectedDeparture(null)}
              className={`flex items-center gap-2.5 px-3.5 py-2 rounded-[14px] border text-[13.5px] font-medium transition active:scale-95 ${
                darkMode
                  ? 'bg-slate-800/90 border-slate-700 text-slate-100 hover:bg-slate-800'
                  : 'bg-[#F8FAFC] border-[#B8C4D9] text-[#111827] hover:bg-slate-100'
              }`}
            >
              <ArrowLeft className="w-4 h-4 stroke-[2.2]" />
              <span>Szczegóły kursu</span>
            </button>

            <div className="flex items-center gap-1.5">
              <button
                onClick={handleRefresh}
                title="Odśwież pozycję pojazdu"
                className="w-10 h-10 rounded-[12px] bg-[#1D54C8] hover:bg-[#1845A8] active:scale-95 text-white flex items-center justify-center shadow-xs transition"
              >
                <RefreshCw className={`w-4 h-4 ${isRefreshing ? 'animate-spin' : ''}`} />
              </button>
            </div>
          </div>
        ) : (
          <div className="px-3.5 pt-3.5 pb-2 shrink-0">
            <div className="flex items-center gap-2.5">
              <div
                className={`flex-1 flex items-center rounded-full px-3.5 py-2 border transition-all ${
                  darkMode ? 'bg-slate-800/80 border-slate-700' : 'bg-[#F0F4FA] border-[#D0D9E8]'
                }`}
              >
                <Search className={`w-4 h-4 mr-2.5 shrink-0 ${darkMode ? 'text-slate-400' : 'text-[#4B5563]'}`} />
                <input
                  type="text"
                  value={searchQuery}
                  onChange={(e) => setSearchQuery(e.target.value)}
                  placeholder="słupek, przystanek, linia, tabor"
                  className={`w-full text-[13.5px] bg-transparent focus:outline-none ${
                    darkMode ? 'text-slate-100 placeholder-slate-400' : 'text-[#1F2937] placeholder-[#6B7280]'
                  }`}
                />
                {searchQuery && (
                  <button onClick={() => setSearchQuery('')} className="text-slate-400 hover:text-slate-600 p-0.5">
                    <X className="w-4 h-4" />
                  </button>
                )}
              </div>

              <button
                onClick={handleRefresh}
                title="Odśwież odjazdy na żywo (GPS)"
                className="w-11 h-11 rounded-[14px] bg-[#2D50E6] hover:bg-[#2242CC] active:scale-95 text-white flex items-center justify-center shadow-sm transition-all shrink-0"
              >
                <RefreshCw className={`w-5 h-5 ${isRefreshing ? 'animate-spin' : ''}`} />
              </button>
            </div>
          </div>
        )}

        {/* ZAWARTOŚĆ GŁÓWNA */}
        <div className="flex-1 overflow-y-auto px-3.5 py-2 space-y-3 select-none">
          {/* ==================== ZAKŁADKA 1: ODJAZDY / SZCZEGÓŁY KURSU ==================== */}
          {activeTab === 'odjazdy' && selectedDeparture ? (
            <div className="space-y-3 pb-4 animate-fade-in">
              {/* Baner linii i kierunku */}
              <div
                style={{
                  backgroundColor: getLineBadgeStyle(selectedDeparture.line).bg,
                  color: getLineBadgeStyle(selectedDeparture.line).text
                }}
                className="rounded-[22px] px-4 py-3.5 flex items-center gap-3.5 shadow-xs"
              >
                <span className="text-[28px] font-bold leading-none tracking-tight">{selectedDeparture.line}</span>
                <div>
                  <div className="text-[11px] opacity-85 font-medium leading-tight">Kierunek</div>
                  <div className="text-[17px] font-bold leading-snug">{selectedDeparture.direction}</div>
                </div>
              </div>

              {/* Tabor i Brygada */}
              <div className="grid grid-cols-2 gap-2.5">
                <div className={`rounded-[20px] px-4 py-3 ${darkMode ? 'bg-[#1D2432]' : 'bg-[#EAEEF6]'}`}>
                  <div className={`text-[11px] font-medium ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                    Numer taborowy
                  </div>
                  <div className="text-[18px] font-bold leading-tight mt-0.5">
                    {selectedIsOffline ? 'Offline' : selectedDeparture.vehicleId}
                  </div>
                </div>

                <div className={`rounded-[20px] px-4 py-3 ${darkMode ? 'bg-[#1D2432]' : 'bg-[#EAEEF6]'}`}>
                  <div className={`text-[11px] font-medium ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                    Brygada
                  </div>
                  <div className="text-[20px] font-bold leading-tight mt-0.5">
                    {selectedDeparture.brigadeNumber || selectedDeparture.brigade.replace('brygada ', '')}
                  </div>
                </div>
              </div>

              {/* Model i wyposażenie (dla pojazdów offline ukrywamy model i wyposażenie) */}
              <div className={`rounded-[22px] p-3.5 space-y-3 ${darkMode ? 'bg-[#1D2432]' : 'bg-[#EAEEF6]'}`}>
                <div className="flex items-center gap-3">
                  <div
                    className={`w-10 h-10 rounded-[14px] flex items-center justify-center shrink-0 ${
                      selectedIsOffline
                        ? darkMode
                          ? 'bg-slate-800 text-slate-400'
                          : 'bg-slate-200/80 text-slate-500'
                        : darkMode
                        ? 'bg-blue-900/50 text-blue-200'
                        : 'bg-[#CEDDF6] text-[#111827]'
                    }`}
                  >
                    {selectedDeparture.type === 'bus' ? (
                      <Bus className="w-5 h-5 stroke-[2]" />
                    ) : (
                      <TrainFront className="w-5 h-5 stroke-[2]" />
                    )}
                  </div>

                  {selectedIsOffline ? (
                    <div className="min-w-0">
                      <div className={`text-[11px] font-medium ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                        Brak połączenia z pojazdem
                      </div>
                      <div className="text-[14px] font-bold leading-snug">
                        Pojazd nie połączył się z systemem
                      </div>
                      <div className={`text-[11.5px] leading-tight mt-0.5 ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                        Brak danych o modelu i wyposażeniu · czas wg rozkładu
                      </div>
                    </div>
                  ) : (
                    <div className="min-w-0">
                      <div className={`text-[11px] font-medium ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                        Model pojazdu
                      </div>
                      <div className="text-[14.5px] font-bold leading-snug truncate">
                        {selectedDeparture.vehicleModel}
                      </div>
                    </div>
                  )}
                </div>

                {/* Wyposażenie pokazujemy TYLKO gdy pojazd jest połączony (online) */}
                {!selectedIsOffline && selectedDeparture.features && selectedDeparture.features.length > 0 && (
                  <div className="flex items-center gap-2 pt-0.5">
                    {selectedDeparture.features.includes('ac') && (
                      <div
                        title="Klimatyzacja"
                        className={`w-8 h-8 rounded-[10px] border flex items-center justify-center ${
                          darkMode ? 'bg-slate-800 border-slate-700 text-slate-200' : 'bg-white border-[#D5DFEE] text-[#1F2937]'
                        }`}
                      >
                        <Snowflake className="w-4 h-4 stroke-[2]" />
                      </div>
                    )}
                    {selectedDeparture.features.includes('lowfloor') && (
                      <div
                        title="Niska podłoga"
                        className={`w-8 h-8 rounded-[10px] border flex items-center justify-center ${
                          darkMode ? 'bg-slate-800 border-slate-700 text-slate-200' : 'bg-white border-[#D5DFEE] text-[#1F2937]'
                        }`}
                      >
                        <Accessibility className="w-4 h-4 stroke-[2]" />
                      </div>
                    )}
                    {selectedDeparture.features.includes('usb') && (
                      <div
                        title="USB"
                        className={`w-8 h-8 rounded-[10px] border flex items-center justify-center ${
                          darkMode ? 'bg-slate-800 border-slate-700 text-slate-200' : 'bg-white border-[#D5DFEE] text-[#1F2937]'
                        }`}
                      >
                        <Usb className="w-4 h-4 stroke-[2]" />
                      </div>
                    )}
                    {selectedDeparture.features.includes('bike') && (
                      <div
                        title="Rower"
                        className={`w-8 h-8 rounded-[10px] border flex items-center justify-center ${
                          darkMode ? 'bg-slate-800 border-slate-700 text-slate-200' : 'bg-white border-[#D5DFEE] text-[#1F2937]'
                        }`}
                      >
                        <Bike className="w-4 h-4 stroke-[2]" />
                      </div>
                    )}
                    {selectedDeparture.features.includes('ticket') && (
                      <div
                        title="Biletomat"
                        className={`w-8 h-8 rounded-[10px] border flex items-center justify-center ${
                          darkMode ? 'bg-slate-800 border-slate-700 text-slate-200' : 'bg-white border-[#D5DFEE] text-[#1F2937]'
                        }`}
                      >
                        <Ticket className="w-4 h-4 stroke-[2]" />
                      </div>
                    )}
                  </div>
                )}
              </div>

              {/* Trasa na żywo */}
              <div className="flex items-center justify-between gap-3 pt-1 px-0.5">
                <div className="text-[14px] font-bold leading-tight shrink-0">
                  {selectedIsOffline ? (
                    <>
                      Trasa
                      <br />
                      rozkładowa
                    </>
                  ) : (
                    <>
                      Trasa na
                      <br />
                      żywo
                    </>
                  )}
                </div>

                <div
                  className={`flex-1 rounded-[16px] px-3.5 py-2.5 flex items-center gap-2 border ${
                    darkMode ? 'bg-[#1D2432] border-slate-700 text-slate-200' : 'bg-[#EAEEF6] border-[#D1DBEC] text-[#2C3545]'
                  }`}
                >
                  {selectedIsOffline ? (
                    <span
                      className={`w-2.5 h-2.5 rounded-full border-[1.8px] shrink-0 animate-soft-pulse ${
                        darkMode ? 'border-slate-300' : 'border-[#1E242B]'
                      }`}
                    />
                  ) : (
                    <span className="w-2.5 h-2.5 rounded-full bg-[#19A957] shrink-0 animate-soft-blink" />
                  )}
                  <span className="text-[11.5px] font-medium leading-tight">
                    {selectedDeparture.livePillText ||
                      `do Ciebie ${selectedDeparture.displayTime} min · ${selectedDeparture.delayText}`}
                  </span>
                </div>
              </div>

              {/* Karta osi czasu z realnym progresem GPS */}
              <div className={`rounded-[22px] px-3.5 py-4 space-y-3 ${darkMode ? 'bg-[#1D2432]' : 'bg-[#EAEEF6]'}`}>
                {!selectedIsOffline && (
                  <div
                    className={`rounded-[14px] px-3 py-2 border text-[11px] flex flex-col gap-1.5 ${
                      darkMode ? 'bg-slate-800/70 border-slate-700 text-slate-300' : 'bg-white/75 border-[#D5DFEE] text-[#374151]'
                    }`}
                  >
                    <div className="flex items-center justify-between font-semibold">
                      <span>Symulator pozycji GPS pojazdu:</span>
                      <span className="text-[#1D54C8] dark:text-blue-400">
                        {Math.abs(currentPos - userStopIdx) < 0.12
                          ? 'Na Twoim przystanku (Ciastko M3)'
                          : currentPos < userStopIdx
                          ? `W drodze (${(userStopIdx - currentPos).toFixed(1)} przyst. przed Tobą)`
                          : `Za Twoim przystankiem (+${(currentPos - userStopIdx).toFixed(1)} przyst.)`}
                      </span>
                    </div>
                    <input
                      type="range"
                      min={0}
                      max={selectedDeparture.route.length - 1}
                      step={0.05}
                      value={currentPos}
                      onChange={(e) => setLiveVehiclePos(parseFloat(e.target.value))}
                      className="w-full accent-[#1D54C8] cursor-pointer h-1.5"
                    />
                  </div>
                )}

                <div className="relative pt-1">
                  {selectedDeparture.route.map((stopItem, idx) => {
                    const isLast = idx === selectedDeparture.route.length - 1;
                    const lineColor = getLineBadgeStyle(selectedDeparture.line).bg;
                    const isUserStop = idx === userStopIdx;

                    const stopPassed = idx < currentPos - 0.08;
                    const vehicleAtThisStop = !selectedIsOffline && Math.abs(currentPos - idx) <= 0.12;
                    const vehicleInThisSegment =
                      !selectedIsOffline && currentPos > idx + 0.12 && currentPos < idx + 1 - 0.12;

                    let segmentProgressPercent = 0;
                    if (currentPos >= idx + 1 - 0.12) {
                      segmentProgressPercent = 100;
                    } else if (currentPos > idx) {
                      segmentProgressPercent = Math.round(((currentPos - idx) / 1) * 100);
                    }

                    // Kształt 6-sided cookie (ciastko) TYLKO gdy pojazd jest na NASZYM przystanku (<1 min)
                    const showCookieHere = vehicleAtThisStop && Math.abs(currentPos - userStopIdx) <= 0.12;

                    return (
                      <div key={idx} className={`flex items-start gap-3 relative ${isLast ? 'pb-0' : 'pb-6'}`}>
                        {/* Linia bazowa (odcinek niepokonany) */}
                        {!isLast && (
                          <div
                            style={{
                              backgroundColor: darkMode ? '#475569' : '#8E9BAE'
                            }}
                            className="absolute left-[13px] top-4 bottom-0 w-[2px] z-0"
                          />
                        )}

                        {/* Pokonana część trasy:
                            - jeśli pojazd jest offline -> zwykła, stała linia
                            - jeśli online -> wolna, płynna fala M3 Expressive (4.2s) */}
                        {!isLast && segmentProgressPercent > 0 && (
                          selectedIsOffline ? (
                            <div
                              style={{
                                backgroundColor: lineColor,
                                height: `${segmentProgressPercent}%`
                              }}
                              className="absolute left-[13px] top-4 w-[2.5px] opacity-65 z-0"
                            />
                          ) : (
                            <M3VerticalLiveWave color={lineColor} progressPercent={segmentProgressPercent} />
                          )
                        )}

                        {/* Ikona pojazdu w trakcie jazdy pomiędzy przystankami (standardowy squircle) */}
                        {vehicleInThisSegment && (
                          <div
                            style={{ top: `calc(10px + ${segmentProgressPercent * 0.44}px)` }}
                            className="absolute left-[-1px] z-20 transition-all duration-300"
                          >
                            <StandardVehicleMarker color={lineColor} size={28}>
                              {selectedDeparture.type === 'bus' ? (
                                <Bus className="w-3.5 h-3.5 stroke-[2.2]" />
                              ) : (
                                <TrainFront className="w-3.5 h-3.5 stroke-[2.2]" />
                              )}
                            </StandardVehicleMarker>
                          </div>
                        )}

                        {/* Punkt przystanku na osi */}
                        <div className="w-7 flex justify-center shrink-0 z-10">
                          {showCookieHere ? (
                            /* Pojazd jest dokładnie na naszym przystanku -> Kształt 6-Sided Cookie ("Ciastko") */
                            <div className="-mt-1">
                              <M3SixSidedCookie color={lineColor} size={34}>
                                {selectedDeparture.type === 'bus' ? (
                                  <Bus className="w-4 h-4 stroke-[2.2]" />
                                ) : (
                                  <TrainFront className="w-4 h-4 stroke-[2.2]" />
                                )}
                              </M3SixSidedCookie>
                            </div>
                          ) : vehicleAtThisStop ? (
                            /* Pojazd stoi na innym przystanku -> normalny marker */
                            <div className="-mt-0.5">
                              <StandardVehicleMarker color={lineColor} size={28}>
                                {selectedDeparture.type === 'bus' ? (
                                  <Bus className="w-3.5 h-3.5 stroke-[2.2]" />
                                ) : (
                                  <TrainFront className="w-3.5 h-3.5 stroke-[2.2]" />
                                )}
                              </StandardVehicleMarker>
                            </div>
                          ) : isUserStop ? (
                            /* Twój przystanek */
                            <div
                              style={{ borderColor: lineColor }}
                              className={`w-4 h-4 rounded-full mt-0.5 border-[3px] ${
                                darkMode ? 'bg-[#1D2432]' : 'bg-white'
                              }`}
                            />
                          ) : stopPassed ? (
                            /* Przystanek minięty */
                            <div style={{ backgroundColor: lineColor }} className="w-3 h-3 rounded-full mt-1" />
                          ) : (
                            /* Kolejny przystanek */
                            <div
                              className={`w-3 h-3 rounded-full mt-1 border-[2px] ${
                                darkMode ? 'bg-[#1D2432] border-slate-300' : 'bg-white border-[#1E242B]'
                              }`}
                            />
                          )}
                        </div>

                        {/* Opis przystanku */}
                        <div className="flex-1 min-w-0">
                          <div className="flex items-center gap-1.5 flex-wrap">
                            <span
                              className={`text-[13.5px] leading-tight ${
                                isUserStop || vehicleAtThisStop
                                  ? 'font-bold'
                                  : stopPassed
                                  ? 'font-medium opacity-50'
                                  : 'font-semibold'
                              }`}
                            >
                              {stopItem.name}
                            </span>

                            {isUserStop && (
                              <span
                                className={`text-[10px] font-bold px-2 py-0.5 rounded-full ${
                                  darkMode ? 'bg-blue-900/60 text-blue-200' : 'bg-[#CEDDF6] text-[#0D131A]'
                                }`}
                              >
                                Twój przystanek
                              </span>
                            )}
                          </div>

                          <div
                            className={`text-[11px] leading-snug mt-0.5 ${
                              stopPassed && !isUserStop
                                ? 'opacity-45'
                                : darkMode
                                ? 'text-slate-400'
                                : 'text-[#5A6578]'
                            }`}
                          >
                            {vehicleAtThisStop
                              ? isUserStop
                                ? 'Pojazd jest tutaj · Twój przystanek'
                                : 'Pojazd jest teraz na tym przystanku'
                              : vehicleInThisSegment
                              ? `Pojazd w drodze do: ${selectedDeparture.route[idx + 1]?.name}`
                              : stopItem.subtitle}
                          </div>

                          {isUserStop && (
                            <div
                              className={`flex items-center gap-1.5 mt-1 text-[11.5px] font-bold ${
                                selectedIsOffline
                                  ? darkMode
                                    ? 'text-slate-300'
                                    : 'text-[#4B5563]'
                                  : 'text-[#148444] dark:text-emerald-400'
                              }`}
                            >
                              {selectedIsOffline ? (
                                <span
                                  className={`w-2 h-2 rounded-full border-[1.6px] ${
                                    darkMode ? 'border-slate-300' : 'border-[#1E242B]'
                                  }`}
                                />
                              ) : (
                                <span className="w-2 h-2 rounded-full bg-[#19A957] animate-soft-blink" />
                              )}
                              <span>{selectedDeparture.delayText}</span>
                            </div>
                          )}
                        </div>

                        {/* Godzina */}
                        <div className={`text-right shrink-0 ${stopPassed && !isUserStop ? 'opacity-50' : ''}`}>
                          <div className={`text-[13px] leading-tight ${isUserStop || vehicleAtThisStop ? 'font-bold' : 'font-semibold'}`}>
                            {stopItem.clock}
                          </div>
                          <div
                            className={`text-[10.5px] mt-0.5 ${
                              isUserStop || vehicleAtThisStop ? 'font-semibold' : darkMode ? 'text-slate-400' : 'text-[#5A6578]'
                            }`}
                          >
                            {vehicleAtThisStop ? 'teraz' : stopPassed ? 'odjechał' : stopItem.etaLabel}
                          </div>
                        </div>
                      </div>
                    );
                  })}
                </div>
              </div>

              {/* Mapa */}
              <div className="pt-1 space-y-1.5">
                <div className={`text-[11.5px] font-medium px-1 ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                  Gdzie jest pojazd
                </div>

                <div
                  className={`rounded-[22px] p-3 border space-y-2.5 ${
                    darkMode ? 'bg-[#1D2432] border-slate-800' : 'bg-[#F5F8FC] border-[#D8E1EE]'
                  }`}
                >
                  <div className="relative w-full h-[168px] rounded-[16px] overflow-hidden border border-[#CBD5E1] bg-[#E3ECF8]">
                    <svg
                      viewBox="0 0 360 170"
                      className="w-full h-full object-cover transition-transform duration-300"
                      style={{ transform: `scale(${mapZoom})` }}
                    >
                      <rect width="360" height="170" fill="#DCE6F5" />
                      <rect x="12" y="18" width="82" height="44" rx="6" fill="#CBE6C8" />
                      <rect x="210" y="112" width="135" height="48" rx="6" fill="#CBE6C8" />
                      <path
                        d="M 0 115 Q 140 95, 230 120 T 360 105 L 360 140 Q 240 155, 130 135 T 0 145 Z"
                        fill="#A7D3F2"
                      />
                      <line x1="0" y1="38" x2="360" y2="38" stroke="#FFFFFF" strokeWidth="6" />
                      <line x1="0" y1="78" x2="360" y2="78" stroke="#FFFFFF" strokeWidth="8" />
                      <line x1="0" y1="138" x2="360" y2="138" stroke="#FFFFFF" strokeWidth="6" />
                      <line x1="56" y1="0" x2="56" y2="170" stroke="#FFFFFF" strokeWidth="6" />
                      <line x1="128" y1="0" x2="128" y2="170" stroke="#FFFFFF" strokeWidth="8" />
                      <line x1="215" y1="0" x2="215" y2="170" stroke="#FFFFFF" strokeWidth="7" />
                      <line x1="295" y1="0" x2="295" y2="170" stroke="#FFFFFF" strokeWidth="6" />
                      <polyline
                        points="24,142 92,142 92,102 152,102 152,78 215,78 215,52 295,52 295,24 342,24"
                        fill="none"
                        stroke={getLineBadgeStyle(selectedDeparture.line).bg}
                        strokeWidth="4.5"
                        strokeDasharray={selectedIsOffline ? '6 4' : 'none'}
                        strokeLinecap="round"
                        strokeLinejoin="round"
                      />
                      <circle
                        cx="92"
                        cy="142"
                        r="4"
                        fill="#FFFFFF"
                        stroke={getLineBadgeStyle(selectedDeparture.line).bg}
                        strokeWidth="2.5"
                      />
                      <circle
                        cx="215"
                        cy="52"
                        r="4"
                        fill="#FFFFFF"
                        stroke={getLineBadgeStyle(selectedDeparture.line).bg}
                        strokeWidth="2.5"
                      />
                      <circle
                        cx="295"
                        cy="24"
                        r="4"
                        fill="#FFFFFF"
                        stroke={getLineBadgeStyle(selectedDeparture.line).bg}
                        strokeWidth="2.5"
                      />
                    </svg>

                    <div className="absolute left-[42%] top-[44%] -translate-x-1/2 -translate-y-1/2 flex flex-col items-center pointer-events-none">
                      <div className="bg-white text-[#111827] text-[10.5px] font-bold px-2.5 py-0.5 rounded-full shadow-md border border-slate-200 mb-1 whitespace-nowrap">
                        {selectedIsOffline
                          ? 'Brak pozycji GPS (rozkładowo)'
                          : Math.abs(currentPos - userStopIdx) <= 0.12
                          ? 'Przy Tobie'
                          : `Za ${selectedDeparture.displayTime} min`}
                      </div>
                      {!selectedIsOffline && Math.abs(currentPos - userStopIdx) <= 0.12 ? (
                        <M3SixSidedCookie color={getLineBadgeStyle(selectedDeparture.line).bg} size={36}>
                          {selectedDeparture.type === 'bus' ? (
                            <Bus className="w-4 h-4 stroke-[2.2]" />
                          ) : (
                            <TrainFront className="w-4 h-4 stroke-[2.2]" />
                          )}
                        </M3SixSidedCookie>
                      ) : (
                        <StandardVehicleMarker color={getLineBadgeStyle(selectedDeparture.line).bg} size={32}>
                          {selectedDeparture.type === 'bus' ? (
                            <Bus className="w-4 h-4 stroke-[2.2]" />
                          ) : (
                            <TrainFront className="w-4 h-4 stroke-[2.2]" />
                          )}
                        </StandardVehicleMarker>
                      )}
                    </div>

                    <div className="absolute bottom-1.5 right-2 bg-white/90 text-[#374151] text-[9.5px] px-1.5 py-0.5 rounded shadow-2xs">
                      © OpenStreetMap
                    </div>
                  </div>

                  <div className="flex items-center gap-2">
                    <button
                      onClick={() => setMapZoom((z) => Math.min(z + 0.2, 1.8))}
                      className={`w-9 h-9 rounded-[10px] border flex items-center justify-center font-bold transition active:scale-95 ${
                        darkMode ? 'bg-slate-800 border-slate-700 text-white' : 'bg-white border-[#CBD5E1] text-[#111827]'
                      }`}
                    >
                      <Plus className="w-4 h-4" />
                    </button>

                    <button
                      onClick={() => setMapZoom((z) => Math.max(z - 0.2, 1))}
                      className={`w-9 h-9 rounded-[10px] border flex items-center justify-center font-bold transition active:scale-95 ${
                        darkMode ? 'bg-slate-800 border-slate-700 text-white' : 'bg-white border-[#CBD5E1] text-[#111827]'
                      }`}
                    >
                      <Minus className="w-4 h-4" />
                    </button>

                    <button
                      onClick={() => {
                        setMapZoom(1);
                        showToast('Wycentrowano na lokalizacji');
                      }}
                      className={`flex-1 h-9 rounded-[10px] border flex items-center justify-center gap-1.5 text-[12.5px] font-semibold transition active:scale-98 ${
                        darkMode ? 'bg-slate-800 border-slate-700 text-white' : 'bg-white border-[#CBD5E1] text-[#111827]'
                      }`}
                    >
                      <MapPin className="w-3.5 h-3.5" />
                      <span>Moja lokalizacja</span>
                    </button>
                  </div>
                </div>
              </div>
            </div>
          ) : activeTab === 'odjazdy' ? (
            <>
              {/* Najbliższy Przystanek */}
              <div
                onClick={() => setShowStopSelectorModal(true)}
                className={`rounded-[22px] px-4 py-3.5 cursor-pointer transition-all active:scale-[0.99] flex items-center justify-between ${
                  darkMode ? 'bg-[#1E2E4F] hover:bg-[#24375E] text-white' : 'bg-[#CEDDF6] hover:bg-[#C3D5F4] text-[#0D131A]'
                }`}
              >
                <div>
                  <div
                    className={`text-[12px] font-medium leading-tight mb-0.5 flex items-center gap-1.5 ${
                      darkMode ? 'text-blue-200' : 'text-[#2D3A4B]'
                    }`}
                  >
                    <span>Najbliższy Przystanek</span>
                    <span className="text-[10px] opacity-75">• dotknij, aby zmienić</span>
                  </div>
                  <h1 className="text-[20px] font-bold tracking-tight leading-snug">{currentStop.name}</h1>
                </div>

                <button
                  onClick={(e) => {
                    e.stopPropagation();
                    toggleFavoriteStop(currentStop.id);
                  }}
                  className={`p-2 rounded-full transition-colors ${
                    favorites.includes(currentStop.id)
                      ? 'text-amber-500'
                      : darkMode
                      ? 'text-blue-200/60 hover:text-white'
                      : 'text-slate-600/70 hover:text-slate-900'
                  }`}
                >
                  <Star className={`w-5 h-5 ${favorites.includes(currentStop.id) ? 'fill-amber-400' : ''}`} />
                </button>
              </div>

              {matchingOtherStops.length > 0 && (
                <div
                  className={`rounded-2xl p-3 border text-xs space-y-2 ${
                    darkMode ? 'bg-slate-800/70 border-slate-700' : 'bg-blue-50/80 border-blue-200'
                  }`}
                >
                  <div className="font-semibold text-blue-600 dark:text-blue-400">
                    Znaleziono inne przystanki pasujące do „{searchQuery}”:
                  </div>
                  <div className="flex flex-wrap gap-1.5">
                    {matchingOtherStops.map((stop) => (
                      <button
                        key={stop.id}
                        onClick={() => {
                          setSelectedStopId(stop.id);
                          setSearchQuery('');
                          showToast(`Przełączono na: ${stop.name}`);
                        }}
                        className="px-2.5 py-1.5 rounded-xl bg-blue-600 text-white font-medium flex items-center gap-1 hover:bg-blue-700 transition"
                      >
                        <MapPin className="w-3 h-3" />
                        {stop.name}
                      </button>
                    ))}
                  </div>
                </div>
              )}

              {/* Lista odjazdów na głównym ekranie */}
              <div className="space-y-3 pt-0.5">
                {filteredDepartures.length === 0 ? (
                  <div className={`rounded-[22px] p-8 text-center ${darkMode ? 'bg-slate-800/60' : 'bg-[#EAEEF6]'}`}>
                    <p className="text-sm font-semibold mb-1">Brak odjazdów na tym przystanku</p>
                    <p className="text-xs opacity-70 mb-3">Wybierz inny przystanek z listy lub zmień kryteria.</p>
                    <button
                      onClick={() => setSearchQuery('')}
                      className="px-4 py-2 rounded-xl bg-[#2D50E6] text-white text-xs font-semibold"
                    >
                      Wyczyść wyszukiwanie
                    </button>
                  </div>
                ) : (
                  filteredDepartures.map((dep) => {
                    const badgeStyle = getLineBadgeStyle(dep.line);
                    const depOffline = isDepartureOffline(dep);

                    return (
                      <div
                        key={dep.id}
                        onClick={() => {
                          setMapZoom(1);
                          setSelectedDeparture(dep);
                        }}
                        className={`rounded-[22px] px-3.5 py-3 flex items-center justify-between cursor-pointer transition-all active:scale-[0.98] ${
                          darkMode ? 'bg-[#1D2432] hover:bg-[#232C3D]' : 'bg-[#EAEEF6] hover:bg-[#E2E8F3]'
                        }`}
                      >
                        <div className="flex items-center gap-3.5 min-w-0">
                          <div
                            style={{ backgroundColor: badgeStyle.bg, color: badgeStyle.text }}
                            className="w-[46px] h-[46px] rounded-[15px] flex items-center justify-center font-bold text-[16px] shrink-0 shadow-xs relative"
                          >
                            {dep.line}
                          </div>

                          <div className="min-w-0">
                            <div
                              className={`text-[15px] font-medium leading-snug truncate ${
                                darkMode ? 'text-slate-100' : 'text-[#111827]'
                              }`}
                            >
                              {dep.direction}
                            </div>

                            <div className="flex items-center flex-wrap mt-1 text-[12px]">
                              {/* Tło ikon wyposażenia jest przezroczyste i zabarwione kolorem danej linii */}
                              {renderFeatureIconsMain(dep.features, depOffline, badgeStyle.bg)}
                              <span className={`truncate ${darkMode ? 'text-slate-400' : 'text-[#374151]'}`}>
                                {dep.brigade}
                                {!depOffline && dep.vehicleId ? ` · ${dep.vehicleId}` : ''}
                              </span>
                            </div>
                          </div>
                        </div>

                        <div className="flex flex-col items-end justify-center pl-2 shrink-0">
                          <div className="flex items-center">
                            {renderStatusDot(dep.status)}
                            <span
                              className={`text-[19px] font-bold leading-none tracking-tight ${
                                darkMode ? 'text-white' : 'text-[#111827]'
                              }`}
                            >
                              {dep.displayTime}
                            </span>
                          </div>
                          <span className={`text-[11px] mt-1 font-normal ${darkMode ? 'text-slate-400' : 'text-[#4B5563]'}`}>
                            min
                          </span>
                        </div>
                      </div>
                    );
                  })
                )}
              </div>

              <div className="pt-2 pb-4 flex items-center justify-between px-2 text-[11px] opacity-60">
                <span className="flex items-center gap-1">
                  <Radio className="w-3 h-3 text-emerald-500 animate-soft-blink" />
                  ZTM Poznań GTFS-RT Live
                </span>
                <span>
                  {lastUpdated.toLocaleTimeString('pl-PL', { hour: '2-digit', minute: '2-digit', second: '2-digit' })}
                </span>
              </div>
            </>
          ) : null}

          {/* ==================== ZAKŁADKA 2: PRZYSTANKI (WSZYSTKIE PRZYSTANKI W POZNANIU + IKONA MAPIPIN) ==================== */}
          {activeTab === 'przystanki' && (
            <div className="space-y-3 pt-1 pb-6">
              <div className="px-1 text-xs opacity-75 font-medium">
                Wszystkie przystanki w Poznaniu ({stopsState.length}) · Kliknij, aby rozwinąć odjazdy
              </div>

              {stopsState
                .filter((s) => {
                  if (!searchQuery) return true;
                  const q = searchQuery.toLowerCase();
                  return (
                    s.name.toLowerCase().includes(q) ||
                    s.code.toLowerCase().includes(q) ||
                    s.location.toLowerCase().includes(q) ||
                    s.lines.some((l) => l.toLowerCase().includes(q))
                  );
                })
                .map((stop) => {
                  const isExpanded = expandedStopId === stop.id;

                  return (
                    <div
                      key={stop.id}
                      className={`rounded-[22px] border transition-all overflow-hidden ${
                        darkMode ? 'bg-[#1D2432] border-slate-800' : 'bg-[#EAEEF6] border-[#D6DFEE]'
                      }`}
                    >
                      {/* Nagłówek przystanku - kółko ma ikonę MapPin */}
                      <div
                        onClick={() => {
                          setExpandedStopId(isExpanded ? null : stop.id);
                          setSelectedStopId(stop.id);
                        }}
                        className="p-3.5 flex items-center justify-between cursor-pointer"
                      >
                        <div className="flex items-center gap-3.5 min-w-0">
                          <div
                            className={`w-[42px] h-[42px] rounded-full flex items-center justify-center shrink-0 ${
                              darkMode ? 'bg-[#25355A] text-blue-100' : 'bg-[#CEDDF6] text-[#1D54C8]'
                            }`}
                          >
                            <MapPin className="w-5 h-5 stroke-[2]" />
                          </div>

                          <div className="min-w-0">
                            <div className="flex items-center gap-2">
                              <span className={`text-[15.5px] font-bold leading-tight truncate ${darkMode ? 'text-slate-100' : 'text-[#111827]'}`}>
                                {stop.name}
                              </span>
                              <span className="text-[11px] px-2 py-0.5 rounded-full bg-black/10 dark:bg-white/10 font-mono">
                                {stop.code}
                              </span>
                            </div>
                            <div className={`text-[12px] leading-snug mt-0.5 truncate ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                              {stop.location} · {stop.distanceText}
                            </div>
                          </div>
                        </div>

                        <div className="flex items-center gap-2 shrink-0">
                          <div className="flex gap-1">
                            {stop.lines.slice(0, 3).map((ln) => {
                              const st = getLineBadgeStyle(ln);
                              return (
                                <span
                                  key={ln}
                                  style={{ backgroundColor: st.bg, color: st.text }}
                                  className="w-6 h-6 rounded-lg text-[11px] font-bold flex items-center justify-center"
                                >
                                  {ln}
                                </span>
                              );
                            })}
                          </div>
                          <ChevronDown className={`w-5 h-5 transition-transform ${isExpanded ? 'rotate-180' : ''} ${darkMode ? 'text-slate-400' : 'text-slate-600'}`} />
                        </div>
                      </div>

                      {/* Rozwijana lista pojazdów tylko dla tego przystanku */}
                      {isExpanded && (
                        <div className={`px-3.5 pb-3.5 pt-1 space-y-2.5 border-t ${darkMode ? 'border-slate-800 bg-slate-900/40' : 'border-[#DCE4F0] bg-white/60'}`}>
                          <div className="text-[11px] font-semibold opacity-70 pt-1">
                            Odjazdy na żywo z przystanku {stop.name}:
                          </div>

                          {stop.departures.length === 0 ? (
                            <div className="text-xs opacity-60 py-3 text-center">
                              Brak zarejestrowanych kursów na tym przystanku w tej chwili.
                            </div>
                          ) : (
                            stop.departures.map((dep) => {
                              const badgeStyle = getLineBadgeStyle(dep.line);
                              const depOffline = isDepartureOffline(dep);

                              return (
                                <div
                                  key={dep.id}
                                  onClick={() => {
                                    setSelectedStopId(stop.id);
                                    setMapZoom(1);
                                    setSelectedDeparture(dep);
                                    setActiveTab('odjazdy');
                                  }}
                                  className={`rounded-[18px] px-3.5 py-2.5 flex items-center justify-between cursor-pointer transition active:scale-[0.98] ${
                                    darkMode ? 'bg-[#181F2C] hover:bg-slate-800' : 'bg-white hover:bg-slate-50 border border-slate-200/80'
                                  }`}
                                >
                                  <div className="flex items-center gap-3 min-w-0">
                                    <div
                                      style={{ backgroundColor: badgeStyle.bg, color: badgeStyle.text }}
                                      className="w-10 h-10 rounded-[12px] flex items-center justify-center font-bold text-[14px] shrink-0"
                                    >
                                      {dep.line}
                                    </div>
                                    <div className="min-w-0">
                                      <div className={`text-[14px] font-medium leading-snug truncate ${darkMode ? 'text-slate-100' : 'text-[#111827]'}`}>
                                        {dep.direction}
                                      </div>
                                      <div className="flex items-center flex-wrap mt-0.5 text-[11.5px]">
                                        {renderFeatureIconsMain(dep.features, depOffline, badgeStyle.bg)}
                                        <span className={`truncate ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                                          {dep.brigade} {!depOffline && dep.vehicleId ? `· ${dep.vehicleId}` : ''}
                                        </span>
                                      </div>
                                    </div>
                                  </div>

                                  <div className="flex flex-col items-end justify-center pl-2 shrink-0">
                                    <div className="flex items-center">
                                      {renderStatusDot(dep.status)}
                                      <span className={`text-[17px] font-bold leading-none ${darkMode ? 'text-white' : 'text-[#111827]'}`}>
                                        {dep.displayTime}
                                      </span>
                                    </div>
                                    <span className="text-[10.5px] opacity-60 mt-0.5">min</span>
                                  </div>
                                </div>
                              );
                            })
                          )}

                          <button
                            onClick={() => {
                              setSelectedStopId(stop.id);
                              setActiveTab('odjazdy');
                            }}
                            className="w-full py-2 rounded-xl bg-[#2D50E6] text-white text-xs font-semibold flex items-center justify-center gap-1.5 mt-1"
                          >
                            <span>Przejdź do pełnego monitora tego przystanku</span>
                            <ChevronRight className="w-4 h-4" />
                          </button>
                        </div>
                      )}
                    </div>
                  );
                })}
            </div>
          )}

          {/* ==================== ZAKŁADKA 3: ULUBIONE (BEZ FILTRA LINII) ==================== */}
          {activeTab === 'ulubione' && (
            <div className="space-y-4 pt-1">
              <div>
                <h2 className="text-base font-bold px-1 mb-2">Ulubione przystanki</h2>
                {favorites.length === 0 ? (
                  <div className={`rounded-[22px] p-6 text-center text-xs opacity-75 ${darkMode ? 'bg-[#1D2432]' : 'bg-[#EAEEF6]'}`}>
                    Brak ulubionych przystanków.
                  </div>
                ) : (
                  <div className="space-y-2.5">
                    {stopsState
                      .filter((s) => favorites.includes(s.id))
                      .map((stop) => (
                        <div
                          key={stop.id}
                          onClick={() => {
                            setSelectedStopId(stop.id);
                            setActiveTab('odjazdy');
                          }}
                          className={`rounded-[22px] p-3.5 flex items-center justify-between cursor-pointer ${
                            darkMode ? 'bg-[#1D2432] hover:bg-[#232C3D]' : 'bg-[#EAEEF6] hover:bg-[#E2E8F3]'
                          }`}
                        >
                          <div className="flex items-center gap-3">
                            <div className="w-10 h-10 rounded-2xl bg-[#2D50E6] text-white flex items-center justify-center">
                              <MapPin className="w-5 h-5" />
                            </div>
                            <div>
                              <div className="font-bold text-sm">{stop.name}</div>
                              <div className="text-xs opacity-70">
                                {stop.departures.length} kursów na żywo
                              </div>
                            </div>
                          </div>
                          <ChevronRight className="w-5 h-5 opacity-60" />
                        </div>
                      ))}
                  </div>
                )}
              </div>
            </div>
          )}

          {/* ==================== ZAKŁADKA 4: USTAWIENIA ==================== */}
          {activeTab === 'ustawienia' && (
            <div className="h-full flex flex-col justify-between pt-3 pb-6 px-1 select-none">
              <div>
                <h1 className={`text-[26px] font-bold tracking-tight mb-4 px-1 ${darkMode ? 'text-white' : 'text-[#111827]'}`}>
                  Ustawienia
                </h1>

                <div className="space-y-3">
                  <button
                    onClick={() => setSettingsSubModal('motyw')}
                    className={`w-full rounded-[24px] px-4 py-4 flex items-center justify-between text-left transition active:scale-[0.99] ${
                      darkMode ? 'bg-[#1D2432] hover:bg-[#232C3D]' : 'bg-[#EAEEF6] hover:bg-[#E2E8F3]'
                    }`}
                  >
                    <div className="flex items-center gap-3.5">
                      <div
                        className={`w-11 h-11 rounded-full flex items-center justify-center shrink-0 ${
                          darkMode ? 'bg-[#25355A] text-blue-100' : 'bg-[#CEDDF6] text-[#111827]'
                        }`}
                      >
                        <Palette className="w-5 h-5 stroke-[1.9]" />
                      </div>
                      <div>
                        <div className="text-[15px] font-semibold leading-snug">Motyw</div>
                        <div className={`text-[12.5px] leading-snug ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                          {darkMode ? 'Ciemny' : 'Jasny'}
                        </div>
                      </div>
                    </div>
                    <ChevronRight className={`w-5 h-5 ${darkMode ? 'text-slate-400' : 'text-[#4B5563]'}`} />
                  </button>

                  <button
                    onClick={() => setSettingsSubModal('dane')}
                    className={`w-full rounded-[24px] px-4 py-4 flex items-center justify-between text-left transition active:scale-[0.99] ${
                      darkMode ? 'bg-[#1D2432] hover:bg-[#232C3D]' : 'bg-[#EAEEF6] hover:bg-[#E2E8F3]'
                    }`}
                  >
                    <div className="flex items-center gap-3.5">
                      <div
                        className={`w-11 h-11 rounded-full flex items-center justify-center shrink-0 ${
                          darkMode ? 'bg-[#25355A] text-blue-100' : 'bg-[#CEDDF6] text-[#111827]'
                        }`}
                      >
                        <Database className="w-5 h-5 stroke-[1.9]" />
                      </div>
                      <div>
                        <div className="text-[15px] font-semibold leading-snug">Informacje o danych</div>
                        <div className={`text-[12.5px] leading-snug ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                          ZTM Poznań GTFS-RT
                        </div>
                      </div>
                    </div>
                    <ChevronRight className={`w-5 h-5 ${darkMode ? 'text-slate-400' : 'text-[#4B5563]'}`} />
                  </button>

                  <button
                    onClick={() => setSettingsSubModal('legenda')}
                    className={`w-full rounded-[24px] px-4 py-4 flex items-center justify-between text-left transition active:scale-[0.99] ${
                      darkMode ? 'bg-[#1D2432] hover:bg-[#232C3D]' : 'bg-[#EAEEF6] hover:bg-[#E2E8F3]'
                    }`}
                  >
                    <div className="flex items-center gap-3.5">
                      <div
                        className={`w-11 h-11 rounded-full flex items-center justify-center shrink-0 ${
                          darkMode ? 'bg-[#25355A] text-blue-100' : 'bg-[#CEDDF6] text-[#111827]'
                        }`}
                      >
                        <List className="w-5 h-5 stroke-[1.9]" />
                      </div>
                      <div>
                        <div className="text-[15px] font-semibold leading-snug">Legenda</div>
                        <div className={`text-[12.5px] leading-snug ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>
                          Kolory kropek i wyposażenie
                        </div>
                      </div>
                    </div>
                    <ChevronRight className={`w-5 h-5 ${darkMode ? 'text-slate-400' : 'text-[#4B5563]'}`} />
                  </button>
                </div>
              </div>

              <div
                onClick={() => setBumpAnimKey((k) => k + 1)}
                className="flex flex-col items-center justify-center pt-10 pb-2 cursor-pointer"
              >
                <div key={bumpAnimKey} className="flex items-center justify-center gap-5 mb-3 h-12">
                  <svg
                    viewBox="0 0 36 28"
                    className="w-9 h-7 text-[#2356A8] dark:text-blue-400 animate-bus-bump"
                    fill="none"
                    stroke="currentColor"
                    strokeWidth="2.2"
                    strokeLinecap="round"
                    strokeLinejoin="round"
                  >
                    <rect x="2" y="4" width="30" height="16" rx="3.5" />
                    <line x1="2" y1="12" x2="32" y2="12" />
                    <line x1="11" y1="4" x2="11" y2="12" />
                    <line x1="21" y1="4" x2="21" y2="12" />
                    <circle cx="9" cy="21.5" r="2.5" fill="currentColor" />
                    <circle cx="25" cy="21.5" r="2.5" fill="currentColor" />
                  </svg>

                  <svg
                    viewBox="0 0 36 28"
                    className="w-9 h-7 text-[#2356A8] dark:text-blue-400 animate-tram-bump"
                    fill="none"
                    stroke="currentColor"
                    strokeWidth="2.2"
                    strokeLinecap="round"
                    strokeLinejoin="round"
                  >
                    <path d="M4 6C4 4.89543 4.89543 4 6 4H26C29.5 4 32 7.5 32 12V18C32 19.1046 31.1046 20 30 20H6C4.89543 20 4 19.1046 4 18V6Z" />
                    <line x1="4" y1="12" x2="32" y2="12" />
                    <line x1="13" y1="4" x2="13" y2="12" />
                    <line x1="22" y1="4" x2="22" y2="12" />
                    <line x1="2" y1="24" x2="34" y2="24" />
                    <circle cx="10" cy="21" r="1.5" fill="currentColor" />
                    <circle cx="24" cy="21" r="1.5" fill="currentColor" />
                  </svg>
                </div>

                <div className={`text-[18px] font-bold tracking-tight leading-snug ${darkMode ? 'text-white' : 'text-[#111827]'}`}>
                  Przystanek Poznań
                </div>
                <div className={`text-[12.5px] mt-0.5 ${darkMode ? 'text-slate-400' : 'text-[#5A6578]'}`}>Wersja 1.0</div>
              </div>
            </div>
          )}
        </div>

        {/* DOLNY PASEK NAWIGACJI */}
        <div className={`px-3 py-2.5 border-t shrink-0 ${darkMode ? 'bg-[#12161F] border-slate-800' : 'bg-[#FAFBFD] border-[#E5EAF2]'}`}>
          <div className="grid grid-cols-4 gap-1">
            <button
              onClick={() => setActiveTab('odjazdy')}
              className="flex flex-col items-center justify-center py-1 rounded-2xl transition-all"
            >
              <div
                className={`px-4 py-1 rounded-full mb-1 flex items-center justify-center transition-colors ${
                  activeTab === 'odjazdy'
                    ? darkMode
                      ? 'bg-[#25355A] text-blue-100'
                      : 'bg-[#CEDDF6] text-[#0D131A]'
                    : darkMode
                    ? 'text-slate-400'
                    : 'text-[#1F2937]'
                }`}
              >
                <Clock className="w-[19px] h-[19px] stroke-[2]" />
              </div>
              <span
                className={`text-[11.5px] leading-none ${
                  activeTab === 'odjazdy'
                    ? 'font-bold text-[#111827] dark:text-white'
                    : 'font-medium text-[#4B5563] dark:text-slate-400'
                }`}
              >
                Odjazdy
              </span>
            </button>

            <button
              onClick={() => {
                setSelectedDeparture(null);
                setActiveTab('przystanki');
              }}
              className="flex flex-col items-center justify-center py-1 rounded-2xl transition-all"
            >
              <div
                className={`px-4 py-1 rounded-full mb-1 flex items-center justify-center transition-colors ${
                  activeTab === 'przystanki'
                    ? darkMode
                      ? 'bg-[#25355A] text-blue-100'
                      : 'bg-[#CEDDF6] text-[#0D131A]'
                    : darkMode
                    ? 'text-slate-400'
                    : 'text-[#1F2937]'
                }`}
              >
                <MapPin className="w-[19px] h-[19px] stroke-[2]" />
              </div>
              <span
                className={`text-[11.5px] leading-none ${
                  activeTab === 'przystanki'
                    ? 'font-bold text-[#111827] dark:text-white'
                    : 'font-medium text-[#4B5563] dark:text-slate-400'
                }`}
              >
                Przystanki
              </span>
            </button>

            <button
              onClick={() => {
                setSelectedDeparture(null);
                setActiveTab('ulubione');
              }}
              className="flex flex-col items-center justify-center py-1 rounded-2xl transition-all"
            >
              <div
                className={`px-4 py-1 rounded-full mb-1 flex items-center justify-center transition-colors ${
                  activeTab === 'ulubione'
                    ? darkMode
                      ? 'bg-[#25355A] text-blue-100'
                      : 'bg-[#CEDDF6] text-[#0D131A]'
                    : darkMode
                    ? 'text-slate-400'
                    : 'text-[#1F2937]'
                }`}
              >
                <Star className="w-[19px] h-[19px] stroke-[2]" />
              </div>
              <span
                className={`text-[11.5px] leading-none ${
                  activeTab === 'ulubione'
                    ? 'font-bold text-[#111827] dark:text-white'
                    : 'font-medium text-[#4B5563] dark:text-slate-400'
                }`}
              >
                Ulubione
              </span>
            </button>

            <button
              onClick={() => {
                setSelectedDeparture(null);
                setActiveTab('ustawienia');
              }}
              className="flex flex-col items-center justify-center py-1 rounded-2xl transition-all"
            >
              <div
                className={`px-4 py-1 rounded-full mb-1 flex items-center justify-center transition-colors ${
                  activeTab === 'ustawienia'
                    ? darkMode
                      ? 'bg-[#25355A] text-blue-100'
                      : 'bg-[#CEDDF6] text-[#0D131A]'
                    : darkMode
                    ? 'text-slate-400'
                    : 'text-[#1F2937]'
                }`}
              >
                <Settings className="w-[19px] h-[19px] stroke-[2]" />
              </div>
              <span
                className={`text-[11.5px] leading-none ${
                  activeTab === 'ustawienia'
                    ? 'font-bold text-[#111827] dark:text-white'
                    : 'font-medium text-[#4B5563] dark:text-slate-400'
                }`}
              >
                Ustawienia
              </span>
            </button>
          </div>
        </div>

        {/* MODALE */}
        {settingsSubModal && (
          <div onClick={() => setSettingsSubModal(null)} className="absolute inset-0 z-40 bg-black/50 backdrop-blur-xs flex items-end justify-center">
            <div onClick={(e) => e.stopPropagation()} className={`w-full rounded-t-[28px] p-5 max-h-[80%] overflow-y-auto shadow-2xl ${darkMode ? 'bg-[#181F2C] text-white' : 'bg-white text-slate-900'}`}>
              <div className="flex items-center justify-between mb-4">
                <h3 className="font-bold text-base">
                  {settingsSubModal === 'motyw' && 'Wybierz motyw'}
                  {settingsSubModal === 'dane' && 'Informacje o danych'}
                  {settingsSubModal === 'legenda' && 'Legenda i wyposażenie'}
                </h3>
                <button onClick={() => setSettingsSubModal(null)} className="p-1.5 rounded-full bg-slate-100 dark:bg-slate-800">
                  <X className="w-4 h-4" />
                </button>
              </div>

              {settingsSubModal === 'motyw' && (
                <div className="space-y-2.5">
                  <button
                    onClick={() => {
                      setDarkMode(false);
                      setSettingsSubModal(null);
                      showToast('Ustawiono motyw: Jasny');
                    }}
                    className={`w-full p-3.5 rounded-2xl flex items-center justify-between font-medium ${!darkMode ? 'bg-[#CEDDF6] text-[#0D131A] font-bold' : 'bg-slate-800 text-slate-200'}`}
                  >
                    <span className="flex items-center gap-2.5"><Sun className="w-4 h-4" /> Jasny</span>
                    {!darkMode && <Check className="w-4 h-4" />}
                  </button>
                  <button
                    onClick={() => {
                      setDarkMode(true);
                      setSettingsSubModal(null);
                      showToast('Ustawiono motyw: Ciemny');
                    }}
                    className={`w-full p-3.5 rounded-2xl flex items-center justify-between font-medium ${darkMode ? 'bg-[#25355A] text-white font-bold' : 'bg-[#EAEEF6] text-slate-800'}`}
                  >
                    <span className="flex items-center gap-2.5"><Moon className="w-4 h-4" /> Ciemny</span>
                    {darkMode && <Check className="w-4 h-4" />}
                  </button>
                </div>
              )}

              {settingsSubModal === 'dane' && (
                <div className="space-y-3 text-xs leading-relaxed opacity-90">
                  <p>Dane o rozkładach jazdy i pozycji GPS pojazdów pochodzą z systemu <strong>ZTM Poznań (GTFS / GTFS-RT)</strong>.</p>
                </div>
              )}

              {settingsSubModal === 'legenda' && (
                <div className="space-y-4 text-xs">
                  <div className="space-y-2">
                    <div className="font-bold text-sm">Status GPS na żywo</div>
                    <div className="flex items-center gap-2.5">{renderStatusDot('realtime-now')} <span>Odjazd za &lt;1 min (delikatne mruganie)</span></div>
                    <div className="flex items-center gap-2.5">{renderStatusDot('realtime-soon')} <span>Odjazd za kilka minut</span></div>
                    <div className="flex items-center gap-2.5">{renderStatusDot('scheduled')} <span>Kurs rozkładowy (offline – brak GPS)</span></div>
                  </div>
                </div>
              )}
            </div>
          </div>
        )}

        {showStopSelectorModal && (
          <div onClick={() => setShowStopSelectorModal(false)} className="absolute inset-0 z-40 bg-black/50 backdrop-blur-xs flex items-end justify-center">
            <div onClick={(e) => e.stopPropagation()} className={`w-full rounded-t-[28px] p-5 max-h-[75%] overflow-y-auto shadow-2xl ${darkMode ? 'bg-[#181F2C] text-white' : 'bg-white text-slate-900'}`}>
              <div className="flex items-center justify-between mb-3">
                <h3 className="font-bold text-base">Wybierz przystanek</h3>
                <button onClick={() => setShowStopSelectorModal(false)} className="p-1.5 rounded-full bg-slate-100 dark:bg-slate-800">
                  <X className="w-4 h-4" />
                </button>
              </div>
              <div className="space-y-2">
                {stopsState.map((s) => (
                  <button
                    key={s.id}
                    onClick={() => {
                      setSelectedStopId(s.id);
                      setShowStopSelectorModal(false);
                      showToast(`Przełączono na: ${s.name}`);
                    }}
                    className={`w-full text-left p-3.5 rounded-2xl flex items-center justify-between transition ${
                      s.id === selectedStopId
                        ? 'bg-[#CEDDF6] text-[#0D131A] font-bold'
                        : darkMode
                        ? 'bg-slate-800/70 hover:bg-slate-800'
                        : 'bg-[#EAEEF6] hover:bg-slate-200'
                    }`}
                  >
                    <div>
                      <div className="text-sm font-bold">{s.name}</div>
                      <div className="text-xs opacity-70">{s.location}</div>
                    </div>
                    <span className="text-xs font-mono px-2 py-1 rounded-lg bg-black/10">{s.code}</span>
                  </button>
                ))}
              </div>
            </div>
          </div>
        )}
      </div>
    </div>
  );
}