import React, { useState, useEffect } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { Input } from "@/components/ui/input"; import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select"; import { motion } from "framer-motion";

export default function DataResellerSite() { const [network, setNetwork] = useState(""); const [amount, setAmount] = useState(""); const [phone, setPhone] = useState(""); const [screenshot, setScreenshot] = useState(null); const [orders, setOrders] = useState([]); const [adminMode, setAdminMode] = useState(false); const [password, setPassword] = useState("");

const ADMIN_PASSWORD = "1234"; // CHANGE THIS BEFORE LAUNCH const WHATSAPP_NUMBER = "23354XXXXXXX"; // Replace with your WhatsApp number (without +)

useEffect(() => { const savedOrders = localStorage.getItem("orders"); if (savedOrders) setOrders(JSON.parse(savedOrders)); }, []);

useEffect(() => { localStorage.setItem("orders", JSON.stringify(orders)); }, [orders]);

const handleOrder = () => { if (!network || !amount || !phone || !screenshot) { return alert("Please complete all fields and upload payment screenshot"); }

const newOrder = {
  id: Date.now(),
  network,
  amount,
  phone,
  status: "Pending",
  date: new Date().toLocaleString()
};

setOrders([newOrder, ...orders]);
setNetwork("");
setAmount("");
setPhone("");
setScreenshot(null);
alert("Order submitted successfully. Await confirmation.");

};

const markDelivered = (id) => { const updated = orders.map((order) => order.id === id ? { ...order, status: "Delivered" } : order ); setOrders(updated); };

const totalProfit = orders .filter((o) => o.status === "Delivered") .reduce((acc, curr) => acc + Number(curr.amount || 0), 0);

if (adminMode && password !== ADMIN_PASSWORD) { return ( <div className="min-h-screen flex items-center justify-center bg-gray-100"> <Card className="p-6 rounded-2xl shadow-xl"> <Input type="password" placeholder="Enter Admin Password" onChange={(e) => setPassword(e.target.value)} /> </Card> </div> ); }

return ( <div className="min-h-screen bg-gray-100 p-6 grid gap-6">

{/* LOGO SECTION */}
  <motion.div
    initial={{ opacity: 0, y: -20 }}
    animate={{ opacity: 1, y: 0 }}
    className="text-center"
  >
    <h1 className="text-4xl font-extrabold">⚡ Ghana Data Hub</h1>
    <p className="text-gray-600">Fast • Affordable • Reliable Data Bundles</p>
  </motion.div>

  {!adminMode && (
    <Card className="max-w-xl mx-auto rounded-2xl shadow-lg">
      <CardContent className="p-6 space-y-4">
        <h2 className="text-xl font-semibold">Buy Data</h2>

        <Select onValueChange={setNetwork} value={network}>
          <SelectTrigger>
            <SelectValue placeholder="Select Network" />
          </SelectTrigger>
          <SelectContent>
            <SelectItem value="MTN">MTN</SelectItem>
            <SelectItem value="Telecel">Telecel</SelectItem>
            <SelectItem value="AirtelTigo">AirtelTigo</SelectItem>
          </SelectContent>
        </Select>

        <Input
          type="number"
          placeholder="Amount Paid (GH₵)"
          value={amount}
          onChange={(e) => setAmount(e.target.value)}
        />

        <Input
          type="tel"
          placeholder="Customer Phone Number"
          value={phone}
          onChange={(e) => setPhone(e.target.value)}
        />

        <div className="bg-gray-50 p-3 rounded-xl text-sm">
          <p className="font-semibold">Payment Instructions:</p>
          <p>1. Send MoMo to: <strong>054XXXXXXX (Your Name)</strong></p>
          <p>2. Upload payment screenshot below.</p>
          <p>3. Click Submit Order.</p>
          <p className="text-green-600 font-medium">Delivery time: 5–15 minutes.</p>
        </div>

        <Input
          type="file"
          onChange={(e) => setScreenshot(e.target.files[0])}
        />

        <Button className="w-full" onClick={handleOrder}>
          Submit Order
        </Button>

        <Button
          variant="outline"
          className="w-full"
          onClick={() => window.open(`https://wa.me/${WHATSAPP_NUMBER}`, "_blank")}
        >
          Chat Us on WhatsApp
        </Button>

        <Button
          variant="outline"
          className="w-full"
          onClick={() => setAdminMode(true)}
        >
          Admin Login
        </Button>
      </CardContent>
    </Card>
  )}

  {adminMode && (
    <Card className="max-w-3xl mx-auto rounded-2xl shadow-lg">
      <CardContent className="p-6">
        <h2 className="text-xl font-semibold mb-4">Admin Dashboard</h2>
        <p className="mb-4 font-medium">Total Delivered Sales: GH₵ {totalProfit}</p>

        {orders.length === 0 && <p>No orders yet.</p>}

        <div className="space-y-3">
          {orders.map((order) => (
            <div
              key={order.id}
              className="p-4 bg-white rounded-xl shadow flex justify-between items-center"
            >
              <div>
                <p className="font-medium">
                  {order.network} - GH₵ {order.amount}
                </p>
                <p className="text-sm text-gray-500">{order.phone}</p>
                <p className="text-xs text-gray-400">{order.date}</p>
              </div>
              <div className="flex gap-2 items-center">
                <span className="text-sm font-semibold">
                  {order.status}
                </span>
                {order.status === "Pending" && (
                  <Button size="sm" onClick={() => markDelivered(order.id)}>
                    Mark Delivered
                  </Button>
                )}
              </div>
            </div>
          ))}
        </div>

        <Button
          variant="outline"
          className="w-full mt-6"
          onClick={() => setAdminMode(false)}
        >
          Back to Store
        </Button>
      </CardContent>
    </Card>
  )}

  {/* TESTIMONIAL SECTION */}
  {!adminMode && (
    <Card className="max-w-xl mx-auto rounded-2xl shadow-md">
      <CardContent className="p-6">
        <h3 className="text-lg font-semibold mb-2">What Our Customers Say</h3>
        <p className="text-sm text-gray-600">“Very fast delivery! I received my data in 3 minutes.”</p>
        <p className="text-sm text-gray-600">“Trusted and reliable. Highly recommended.”</p>
      </CardContent>
    </Card>
  )}
</div>

); }
