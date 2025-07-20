# Codealpha-internship--project
This repository contains my project for internship in codealpha.
// Bus pass
import { useState } from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Button } from '@/components/ui/button';
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs';
import { Badge } from '@/components/ui/badge';
import { Alert, AlertDescription } from '@/components/ui/alert';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import { 
  Bus,
  Shield, 
  CreditCard, 
  Ticket, 
  BarChart3,
  Users,
  AlertTriangle,
  CheckCircle,
  TrendingUp,
  Clock,
  MapPin,
  Zap,
  Wifi,
  Route as RouteIcon,
  Settings,
  Bell,
  Activity
} from 'lucide-react';
import { RouteSelector } from '@/components/route-selector';
import { Toaster } from '@/components/ui/toaster';
import { useToast } from '@/hooks/use-toast';
import { BusRoute, BusSchedule, BookingFormData, Ticket as TicketType } from '@/types/bus-system';
import { mockSystemMetrics, mockTickets, mockFraudAlerts, mockBuses } from '@/data/mock-bus-data';
import { generateSecureQRCode, generateValidationCode, detectDuplicateBooking } from '@/utils/fraud-detection';

function App() {
  const [activeTab, setActiveTab] = useState('booking');
  const [currentBooking, setCurrentBooking] = useState<{
    route: BusRoute;
    schedule: BusSchedule;
    bookingData: BookingFormData;
  } | null>(null);
  const [userTickets, setUserTickets] = useState<TicketType[]>(mockTickets);
  const { toast } = useToast();

  const handleRouteSelection = (route: BusRoute, schedule: BusSchedule, bookingData: BookingFormData) => {
    setCurrentBooking({ route, schedule, bookingData });
    
    // Simulate fraud detection
    const fraudCheck = detectDuplicateBooking(
      {
        passengerId: 'passenger-001', // Mock current user
        routeId: route.id,
        travelDate: bookingData.travelDate,
        scheduleId: schedule.id
      },
      userTickets
    );

    if (!fraudCheck.isValid) {
      toast({
        title: "Security Alert",
        description: fraudCheck.alerts[0],
        variant: "destructive",
      });
      return;
    }

    // Continue to payment simulation
    simulatePaymentProcess();
  };

  const simulatePaymentProcess = () => {
    if (!currentBooking) return;
// Simulate successful booking
    setTimeout(() => {
      const newTicket: TicketType = {
        id: `ticket-${Date.now()}`,
        ticketNumber: `TKT-2024-${String(Math.floor(Math.random() * 1000000)).padStart(6, '0')}`,
        passengerId: 'passenger-001',
        routeId: currentBooking.route.id,
        scheduleId: currentBooking.schedule.id,
        boardingStop: currentBooking.bookingData.fromStop,
        destinationStop: currentBooking.bookingData.toStop,
        ticketType: 'regular',
        price: currentBooking.schedule.price.regular,
        bookingDate: new Date(),
        travelDate: currentBooking.bookingData.travelDate,
        status: 'active',
        qrCode: generateSecureQRCode({ ticketNumber: `TKT-2024-${Date.now()}` }),
        paymentId: `payment-${Date.now()}`,
        fraudScore: 0.05,
        validationCode: generateValidationCode()
      };

      setUserTickets(prev => [...prev, newTicket]);
      setActiveTab('tickets');
      
      toast({
        title: "Booking Confirmed! 🎉",
        description: `Your ticket ${newTicket.ticketNumber} has been booked successfully.`,
      });

      setCurrentBooking(null);
    }, 2000);

    toast({
      title: "Processing Payment...",
      description: "Please wait while we process your booking.",
    });
  };

  return (
    <div className="min-h-screen bg-gray-50">
      {/* Header */}
      <div className="border-b bg-white shadow-sm">
        <div className="max-w-7xl mx-auto px-4 py-4">
          <div className="flex items-center justify-between">
            <div>
              <h1 className="text-2xl font-bold flex items-center gap-2">
                <Bus className="h-6 w-6 text-blue-600" />
                CloudBus Pass System
              </h1>
              <p className="text-muted-foreground">
                Secure, scalable, and fraud-resistant ticket booking platform
              </p>
            </div>
            <div className="flex items-center gap-4">
              <Badge variant="outline" className="text-green-600 border-green-600">
                <Activity className="h-3 w-3 mr-1" />
                System Online
              </Badge>
              <Button size="sm" variant="outline">
                <Bell className="h-4 w-4 mr-2" />
                Notifications
              </Button>
              <Avatar>
                <AvatarImage src="/assets/images/default.svg" />
                <AvatarFallback>AJ</AvatarFallback>
              </Avatar>
            </div>
          </div>
        </div>
      </div>

      <div className="max-w-7xl mx-auto px-4 py-6">
        <Tabs value={activeTab} onValueChange={setActiveTab} className="space-y-6">
          <TabsList className="grid w-full grid-cols-5">
            <TabsTrigger value="booking" className="flex items-center gap-2">
              <Ticket className="h-4 w-4" />
              Book Ticket
            </TabsTrigger>
            <TabsTrigger value="tickets" className="flex items-center gap-2">
              <CreditCard className="h-4 w-4" />
              My Tickets
            </TabsTrigger>
            <TabsTrigger value="routes" className="flex items-center gap-2">
              <RouteIcon className="h-4 w-4" />
              Routes & Schedules
            </TabsTrigger>
            <TabsTrigger value="security" className="flex items-center gap-2">
              <Shield className="h-4 w-4" />
              Security
            </TabsTrigger>
            <TabsTrigger value="analytics" className="flex items-center gap-2">
              <BarChart3 className="h-4 w-4" />
              System Analytics
            </TabsTrigger>
          </TabsList>

          {/* Booking Tab */}
          <TabsContent value="booking" className="space-y-6">
            <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
              <div className="lg:col-span-2">
                <RouteSelector onRouteSelect={handleRouteSelection} />
              </div>
              
              <div className="space-y-4">
                <Card>
                  <CardHeader>
                    <CardTitle className="text-lg">System Features</CardTitle>
                  </CardHeader>
                  <CardContent className="space-y-3">
                    <div className="flex items-center gap-2 text-sm">
                      <Shield className="h-4 w-4 text-green-500" />
                      <span>Advanced fraud protection</span>
                    </div>
                    <div className="flex items-center gap-2 text-sm">
                      <Zap className="h-4 w-4 text-yellow-500" />
                      <span>Auto-scaling cloud infrastructure</span>
                    </div>
                    <div className="flex items-center gap-2 text-sm">
                      <Wifi className="h-4 w-4 text-blue-500" />
<span>Real-time seat availability</span>
                    </div>
                    <div className="flex items-center gap-2 text-sm">
                      <CheckCircle className="h-4 w-4 text-green-500" />
                      <span>Digital ticket validation</span>
                    </div>
                  </CardContent>
                </Card>

                <Card>
                  <CardHeader>
                    <CardTitle className="text-lg">Quick Stats</CardTitle>
                  </CardHeader>
                  <CardContent className="space-y-3">
                    <div className="flex justify-between">
                      <span className="text-sm text-muted-foreground">Active Routes</span>
                      <span className="font-medium">12</span>
                    </div>
                    <div className="flex justify-between">
                      <span className="text-sm text-muted-foreground">Available Buses</span>
                      <span className="font-medium">45</span>
                    </div>
                    <div className="flex justify-between">
                      <span className="text-sm text-muted-foreground">System Uptime</span>
                      <span className="font-medium text-green-600">99.9%</span>
                    </div>
                    <div className="flex justify-between">
                      <span className="text-sm text-muted-foreground">Fraud Prevented</span>
                      <span className="font-medium text-orange-600">{mockSystemMetrics.fraudPrevented}</span>
                    </div>
                  </CardContent>
                </Card>
              </div>
            </div>
          </TabsContent>

          {/* My Tickets Tab */}
          <TabsContent value="tickets" className="space-y-6">
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
              {userTickets.map(ticket => (
                <Card key={ticket.id} className="border-l-4 border-l-blue-500">
                  <CardHeader className="pb-3">
                    <div className="flex items-center justify-between">
                      <CardTitle className="text-lg">{ticket.ticketNumber}</CardTitle>
                      <Badge 
                        variant={
                          ticket.status === 'active' ? 'default' : 
                          ticket.status === 'used' ? 'secondary' : 'destructive'
                        }
                      >
                        {ticket.status}
                      </Badge>
                    </div>
                  </CardHeader>
                  <CardContent className="space-y-3">
                    <div className="flex items-center gap-2 text-sm">
                      <MapPin className="h-4 w-4 text-muted-foreground" />
                      <span>{ticket.boardingStop} → {ticket.destinationStop}</span>
                    </div>
                    <div className="flex items-center gap-2 text-sm">
                      <Clock className="h-4 w-4 text-muted-foreground" />
                      <span>{ticket.travelDate.toLocaleDateString()}</span>
                    </div>
                    <div className="flex items-center justify-between">
                      <span className="text-lg font-bold text-green-600">${ticket.price}</span>
                      {ticket.seatNumber && (
                        <span className="text-sm font-medium">Seat {ticket.seatNumber}</span>
                      )}
                    </div>
                    <div className="pt-2 border-t">
                      <div className="flex items-center justify-between text-xs text-muted-foreground">
                        <span>QR: {ticket.qrCode.slice(0, 12)}...</span>
                        <span>Valid: {ticket.validationCode}</span>
                      </div>
                    </div>
                  </CardContent>
                </Card>
              ))}
            </div>
          </TabsContent>

          {/* Routes & Schedules Tab */}
          <TabsContent value="routes" className="space-y-6">
            <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
              {mockBuses.map(bus => (
                <Card key={bus.id}>
                  <CardHeader>
                    <div className="flex items-center justify-between">
                      <CardTitle className="flex items-center gap-2">
                        <Bus className="h-5 w-5" />
                        {bus.busNumber}
                      </CardTitle>
                      <Badge 
                        variant={
                          bus.status === 'active' ? 'default' : 
                          bus.status === 'maintenance' ? 'secondary' : 'destructive'
                        }
                      >
                        {bus.status}
                      </Badge>
                    </div>
                  </CardHeader>
                  <CardContent className="space-y-3">
                    <div className="grid grid-cols-2 gap-4 text-sm">
                      <div>
                        <span className="text-muted-foreground">Model: </span>
                        <span className="font-medium">{bus.model}</span>
                      </div>
<div>
                        <span className="text-muted-foreground">Capacity: </span>
                        <span className="font-medium">{bus.capacity}</span>
                      </div>
                      <div>
                        <span className="text-muted-foreground">Driver: </span>
                        <span className="font-medium">{bus.driver}</span>
                      </div>
                      <div>
                        <span className="text-muted-foreground">Updated: </span>
                        <span className="font-medium">Just now</span>
                      </div>
                    </div>
                    <div className="flex flex-wrap gap-2">
                      {bus.features.map(feature => (
                        <Badge key={feature} variant="outline" className="text-xs">
                          {feature}
                        </Badge>
                      ))}
                    </div>
                  </CardContent>
                </Card>
              ))}
            </div>
          </TabsContent>

          {/* Security Tab */}
          <TabsContent value="security" className="space-y-6">
            <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
              <Card>
                <CardHeader>
                  <CardTitle className="flex items-center gap-2">
                    <Shield className="h-5 w-5" />
                    Fraud Protection Status
                  </CardTitle>
                </CardHeader>
                <CardContent className="space-y-4">
                  <div className="flex items-center justify-between p-3 bg-green-50 rounded-lg border border-green-200">
                    <div>
                      <div className="font-medium text-green-800">All Systems Secure</div>
                      <div className="text-sm text-green-600">Real-time monitoring active</div>
                    </div>
                    <CheckCircle className="h-8 w-8 text-green-600" />
                  </div>
                  
                  <div className="grid grid-cols-2 gap-4">
                    <div className="text-center p-3 border rounded-lg">
                      <div className="text-2xl font-bold text-green-600">99.8%</div>
                      <div className="text-sm text-muted-foreground">Detection Rate</div>
                    </div>
                    <div className="text-center p-3 border rounded-lg">
                      <div className="text-2xl font-bold text-blue-600">&lt; 2s</div>
                      <div className="text-sm text-muted-foreground">Response Time</div>
                    </div>
                  </div>
                </CardContent>
              </Card>

              <Card>
                <CardHeader>
                  <CardTitle>Recent Security Alerts</CardTitle>
                </CardHeader>
                <CardContent className="space-y-3">
                  {mockFraudAlerts.map(alert => (
                    <div key={alert.id} className="p-3 border rounded-lg">
                      <div className="flex items-center justify-between mb-2">
                        <Badge 
                          variant={
                            alert.severity === 'high' ? 'destructive' : 
                            alert.severity === 'medium' ? 'secondary' : 'outline'
                          }
                        >
                          {alert.severity}
                        </Badge>
                        <span className="text-xs text-muted-foreground">
                          {alert.detectedAt.toLocaleTimeString()}
                        </span>
                      </div>
                      <div className="text-sm font-medium">{alert.alertType.replace('_', ' ')}</div>
                      <div className="text-xs text-muted-foreground">{alert.description}</div>
                    </div>
                  ))}
                </CardContent>
              </Card>
            </div>
          </TabsContent>

          {/* Analytics Tab */}
          <TabsContent value="analytics" className="space-y-6">
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
              <Card>
                <CardContent className="p-6">
                  <div className="flex items-center justify-between">
                    <div>
                      <p className="text-sm text-muted-foreground">Total Revenue</p>
                      <p className="text-2xl font-bold">${(mockSystemMetrics.totalRevenue / 1000000).toFixed(1)}M</p>
                    </div>
                    <TrendingUp className="h-8 w-8 text-green-500" />
                  </div>
                  <p className="text-xs text-green-600 mt-2">+12.5% from last month</p>
                </CardContent>
              </Card>

              <Card>
                <CardContent className="p-6">
                  <div className="flex items-center justify-between">
                    <div>
                      <p className="text-sm text-muted-foreground">Tickets Sold</p>
                      <p className="text-2xl font-bold"> {(mockSystemMetrics.totalTicketsSold / 1000).toFixed(0)}K</p>
                    </div>
                    <Ticket className="h-8 w-8 text-blue-500" />
                  </div>
                  <p className="text-xs text-blue-600 mt-2">This month</p>
                </CardContent>
              </Card>

              <Card>
                <CardContent className="p-6">
                  <div className="flex items-center justify-between">
                    <div>
                      <p className="text-sm text-muted-foreground">Active Users</p>
                      <p className="text-2xl font-bold">{(mockSystemMetrics.activeUsers / 1000).toFixed(1)}K</p>
                    </div>
                    <Users className="h-8 w-8 text-purple-500" />
                  </div>
                  <p className="text-xs text-purple-600 mt-2">Currently online</p>
                </CardContent>
              </Card>

              <Card>
<CardContent className="p-6">
                  <div className="flex items-center justify-between">
                    <div>
                      <p className="text-sm text-muted-foreground">System Uptime</p>
                      <p className="text-2xl font-bold">{mockSystemMetrics.systemUptime}%</p>
                    </div>
                    <Activity className="h-8 w-8 text-green-500" />
                  </div>
                  <p className="text-xs text-green-600 mt-2">Last 30 days</p>
                </CardContent>
              </Card>
            </div>

            <Card>
              <CardHeader>
                <CardTitle>Performance Metrics</CardTitle>
              </CardHeader>
              <CardContent>
                <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
                  <div className="text-center p-4 border rounded-lg">
                    <div className="text-3xl font-bold text-blue-600">{mockSystemMetrics.averageLoadFactor}%</div>
                    <p className="text-sm text-muted-foreground">Average Load Factor</p>
                  </div>
                  <div className="text-center p-4 border rounded-lg">
                    <div className="text-3xl font-bold text-green-600">{mockSystemMetrics.onTimePerformance}%</div>
                    <p className="text-sm text-muted-foreground">On-Time Performance</p>
                  </div>
                  <div className="text-center p-4 border rounded-lg">
                    <div className="text-3xl font-bold text-purple-600">{mockSystemMetrics.customerSatisfaction}/5</div>
                    <p className="text-sm text-muted-foreground">Customer Satisfaction</p>
                  </div>
                </div>
              </CardContent>
            </Card>

            <Card>
              <CardHeader>
                <CardTitle>Peak Hours Analysis</CardTitle>
              </CardHeader>
              <CardContent>
                <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                  <div className="p-4 border rounded-lg">
                    <div className="text-lg font-medium mb-2">Morning Peak</div>
                    <div className="text-2xl font-bold text-orange-600">{mockSystemMetrics.peakHours.morning}</div>
                    <div className="text-sm text-muted-foreground">Highest demand period</div>
                  </div>
                  <div className="p-4 border rounded-lg">
                    <div className="text-lg font-medium mb-2">Evening Peak</div>
                    <div className="text-2xl font-bold text-orange-600">{mockSystemMetrics.peakHours.evening}</div>
                    <div className="text-sm text-muted-foreground">Highest demand period</div>
                  </div>
                </div>
              </CardContent>
            </Card>
          </TabsContent>
        </Tabs>
      </div>

      <Toaster />
    </div>
  );
}

export default App;
