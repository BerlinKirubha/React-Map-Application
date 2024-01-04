This application is developed in React JS. Features include spotting the current location, finding possible route of places and track the distance.
please include node_modules directory,I didn't add since it has huge number of files

Replace the Google_API_Key in the application and run it in terminal "npm start"

Install dependencies "npm install"
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
If this dindn't work: Try the below

1)Properly install some React Code Editor(I used VSC).
2)Install Node JS.
3)Open a folder and create react app using the command : npx creat-react-app app_name
                                                         cd app_name
                                                         npm start
4)Update the below changes, don't forget to replace your api key.

//Modify App.js

import React, { useState, useEffect } from 'react';
import { GoogleMap, LoadScript, Marker, DirectionsRenderer } from '@react-google-maps/api';
import './App.css';

const mapContainerStyle = {
  width: '100%',
  height: '400px',
};

const App = () => {
  const [currentLocation, setCurrentLocation] = useState(null);
  const [destinations, setDestinations] = useState([]);
  const [directions, setDirections] = useState(null);

  useEffect(() => {
    // Function to get current location using browser geolocation API
    const getCurrentLocation = () => {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            const { latitude, longitude } = position.coords;
            setCurrentLocation({ lat: latitude, lng: longitude });
          },
          (error) => {
            console.error('Error getting current location:', error);
          }
        );
      } else {
        console.error('Geolocation is not supported by this browser.');
      }
    };

    getCurrentLocation();
  }, []); 

  const handleDirections = () => {
    const directionsService = new window.google.maps.DirectionsService();

    if (destinations.length >= 2) {
      const waypoints = destinations.slice(1, -1).map(destination => ({ location: destination, stopover: true }));

      directionsService.route(
        {
          origin: destinations[0],
          destination: destinations[destinations.length - 1],
          waypoints: waypoints,
          travelMode: 'DRIVING',
        },
        (result, status) => {
          if (status === 'OK') {
            setDirections(result);
          } else {
            alert('Directions request failed due to ' + status);
          }
        }
      );
    } else {
      alert('Please enter at least two destinations.');
    }
  };

  const getDistance = () => {
    if (directions && directions.routes && directions.routes.length > 0) {
      const distance = directions.routes[0].legs.reduce((total, leg) => total + leg.distance.value, 0);
      // Convert meters to kilometers
      const distanceInKm = (distance / 1000).toFixed(2);
      return `Total Distance: ${distanceInKm} km`;
    }
    return '';
  };

  return (
    <LoadScript googleMapsApiKey="Google_API_Key">
      <GoogleMap
        mapContainerStyle={mapContainerStyle}
        center={currentLocation}
        zoom={14}
      >
        {currentLocation && (
          <Marker position={currentLocation} icon={{
            url: 'https://maps.google.com/mapfiles/ms/icons/green-dot.png', 
          }} title="Current Location" />
        )}
        {destinations.map((destination, index) => (
          <Marker key={index} position={{ lat: Number(destination.split(',')[0]), lng: Number(destination.split(',')[1]) }} />
        ))}
        {directions && <DirectionsRenderer directions={directions} />}
      </GoogleMap>

      <div>
        <input
          type="text"
          placeholder="Enter destinations (comma-separated)"
          value={destinations.join(',')}
          onChange={(e) => setDestinations(e.target.value.split(','))}
          style={inputStyle}
        />
        <button onClick={handleDirections} style={buttonStyle}>Get Directions</button>
        <p style={paragraphStyle}>{getDistance()}</p>
      </div>
    </LoadScript>
  );
};

const buttonStyle = {
  backgroundColor: 'Aquamarine',
  color: 'black',
  padding: '10px',
  borderRadius: '5px',
  cursor: 'pointer',
};
const inputStyle = {
  padding: '8px',
  margin: '5px',
  fontSize: '16px',
  borderRadius: '5px',
  border: '1px solid #ccc',
  width: '400px', 
};
const paragraphStyle = {
  color: 'black',         
  fontSize: '16px',      
  fontFamily: 'Arial, sans-serif', 
  lineHeight: '1.5',      
  margin: '10px 0',        
  padding: '10px',  
  borderRadius: '5px',     
  boxShadow: '0 2px 4px rgba(0, 0, 0, 0.1)', 
};

export default App;

*************************************************************************************************************************************

//Modify index.html
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

reportWebVitals();
