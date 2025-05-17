// MoodCam - React Native App (Full Emotion Detection + Image Upload)

import React, { useState } from 'react';
import { View, Text, StyleSheet, Button, Image } from 'react-native';
import { Camera } from 'expo-camera';
import * as FaceDetector from 'expo-face-detector';
import * as ImagePicker from 'expo-image-picker';

export default function App() {
  const [hasPermission, setHasPermission] = useState(null);
  const [cameraRef, setCameraRef] = useState(null);
  const [faceData, setFaceData] = useState([]);
  const [emotions, setEmotions] = useState({});
  const [imageUri, setImageUri] = useState(null);

  React.useEffect(() => {
    (async () => {
      const cameraStatus = await Camera.requestCameraPermissionsAsync();
      const mediaStatus = await ImagePicker.requestMediaLibraryPermissionsAsync();
      setHasPermission(cameraStatus.status === 'granted' && mediaStatus.status === 'granted');
    })();
  }, []);

  const detectEmotions = async (uri) => {
    const result = await FaceDetector.detectFacesAsync(uri, {
      mode: FaceDetector.FaceDetectorMode.fast,
      detectLandmarks: FaceDetector.FaceDetectorLandmarks.none,
      runClassifications: FaceDetector.FaceDetectorClassifications.all,
    });

    if (result.faces.length > 0) {
      const face = result.faces[0];
      const smiling = +(face.smilingProbability * 100).toFixed(1);
      const leftEye = +(face.leftEyeOpenProbability * 100).toFixed(1);
      const rightEye = +(face.rightEyeOpenProbability * 100).toFixed(1);

      const mood = smiling > 70 ? 'Happy' : smiling < 30 ? 'Sad' : 'Neutral';

      setEmotions({
        Happiness: smiling,
        Sadness: +(100 - smiling).toFixed(1),
        Alertness: +(((leftEye + rightEye) / 2).toFixed(1)),
        Mood: mood
      });
    }
  };

  const pickImage = async () => {
    let result = await ImagePicker.launchImageLibraryAsync({
      mediaTypes: ImagePicker.MediaTypeOptions.Images,
      allowsEditing: true,
      quality: 1,
    });

    if (!result.canceled) {
      setImageUri(result.assets[0].uri);
      detectEmotions(result.assets[0].uri);
    }
  };

  const handleFacesDetected = ({ faces }) => {
    setFaceData(faces);
    if (faces.length > 0) {
      const face = faces[0];
      const smiling = +(face.smilingProbability * 100).toFixed(1);
      const leftEye = +(face.leftEyeOpenProbability * 100).toFixed(1);
      const rightEye = +(face.rightEyeOpenProbability * 100).toFixed(1);

      const mood = smiling > 70 ? 'Happy' : smiling < 30 ? 'Sad' : 'Neutral';

      setEmotions({
        Happiness: smiling,
        Sadness: +(100 - smiling).toFixed(1),
        Alertness: +(((leftEye + rightEye) / 2).toFixed(1)),
        Mood: mood
      });
    }
  };

  if (hasPermission === null) return <View />;
  if (hasPermission === false) return <Text style={styles.permissionText}>No access to camera or gallery</Text>;

  return (
    <View style={styles.container}>
      {!imageUri && (
        <Camera
          style={styles.camera}
          type={Camera.Constants.Type.front}
          onFacesDetected={handleFacesDetected}
          faceDetectorSettings={{
            mode: FaceDetector.FaceDetectorMode.fast,
            detectLandmarks: FaceDetector.FaceDetectorLandmarks.none,
            runClassifications: FaceDetector.FaceDetectorClassifications.all,
          }}
          ref={(ref) => setCameraRef(ref)}
        />
      )}

      {imageUri && (
        <Image source={{ uri: imageUri }} style={styles.camera} />
      )}

      <View style={styles.resultContainer}>
        <Text style={styles.moodTitle}>Emotion Analysis</Text>
        {Object.entries(emotions).map(([key, value]) => (
          <Text key={key} style={styles.moodText}>{key}: {value}%</Text>
        ))}
        {!faceData.length && !imageUri && <Text style={styles.moodText}>Scanning...</Text>}
        <Button title="Upload Image" onPress={pickImage} color="#4169E1" />
        {imageUri && <Button title="Back to Camera" onPress={() => { setImageUri(null); setEmotions({}); }} color="#800020" />}
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#191970', // Midnight Blue
  },
  camera: {
    flex: 5,
    width: '100%',
  },
  resultContainer: {
    flex: 2,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#800020', // Burgundy Red
    padding: 20,
  },
  moodTitle: {
    fontSize: 26,
    fontWeight: 'bold',
    color: '#4169E1', // Royal Blue
    marginBottom: 10,
  },
  moodText: {
    fontSize: 18,
    color: '#fff',
    marginVertical: 2,
  },
  permissionText: {
    color: '#800020',
    fontSize: 18,
    textAlign: 'center',
    marginTop: 20,
  },
});
