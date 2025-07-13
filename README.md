# miraa-driver
an app inspired by great miraa drivers 
miraa-driver-safety-app/
├── app/
│   ├── components/
│   │   ├── SpeedMonitor.js
│   │   ├── OvertakeDetector.js
│   │   └── DriverScorecard.js
│   ├── screens/
│   │   ├── DashboardScreen.js
│   │   ├── AlertsScreen.js
│   │   └── SettingsScreen.js
│   ├── services/
│   │   ├── gpsService.js
│   │   ├── alertService.js
│   │   └── apiService.js
│   ├── App.js
│   └── assets/
│       └── icons/
├── backend/
│   ├── controllers/
│   │   ├── driverController.js
│   │   └── alertController.js
│   ├── models/
│   │   ├── Driver.js
│   │   └── Alert.js
│   ├── routes/
│   │   ├── driverRoutes.js
│   │   └── alertRoutes.js
│   ├── server.js
│   └── config/
│       ├── db.js
│       └── auth.js
├── .gitignore
├── README.md
└── package.json
import { PermissionsAndroid, Platform } from 'react-native';
import Geolocation from '@react-native-community/geolocation';

export const getLocation = async () => {
  if (Platform.OS === 'android') {
    const granted = await PermissionsAndroid.request(
      PermissionsAndroid.PERMISSIONS.ACCESS_FINE_LOCATION
    );
    if (granted !== PermissionsAndroid.RESULTS.GRANTED) {
      console.log('Location permission denied');
      return;
    }
  }

  Geolocation.watchPosition(
    position => {
      const { latitude, longitude, speed } = position.coords;
      // Process GPS data
    },
    error => console.log(error),
    { enableHighAccuracy: true, distanceFilter: 10 }
  );
};
import { useEffect, useState } from 'react';
import { getLocation } from '../services/gpsService';

const OvertakeDetector = () => {
  const [isOvertaking, setIsOvertaking] = useState(false);

  useEffect(() => {
    const watchPosition = getLocation(position => {
      const { speed, heading } = position.coords;
      // Implement logic to detect overtaking based on speed and heading
      if (speed > 80 && headingChange > threshold) {
        setIsOvertaking(true);
      } else {
        setIsOvertaking(false);
      }
    });

    return () => watchPosition.remove();
  }, []);

  return (
    <View>
      {isOvertaking && <Text>Unsafe overtaking detected!</Text>}
    </View>
  );
};import PushNotification from 'react-native-push-notification';

export const sendAlert = (message) => {
  PushNotification.localNotification({
    message: message,
    playSound: true,
    soundName: 'default',
  });
};
const Driver = require('../models/Driver');

exports.getDriverStats = async (req, res) => {
  try {
    const stats = await Driver.find({ userId: req.user.id });
    res.json(stats);
  } catch (err) {
    res.status(500).json({ message: 'Server error' });
  }
};

