npx expo init MyTranslator
cd MyTranslator
npm install @react-navigation/native @react-navigation/bottom-tabs react-native-paper
npx expo startimport React, { useState } from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import TranslateScreen from './screens/TranslateScreen';
import FavoritesScreen from './screens/FavoritesScreen';
import HistoryScreen from './screens/HistoryScreen';
import { Provider as PaperProvider } from 'react-native-paper';

const Tab = createBottomTabNavigator();

export default function App() {
  const [favorites, setFavorites] = useState([]);
  const [history, setHistory] = useState([]);

  return (
    <PaperProvider>
      <NavigationContainer>
        <Tab.Navigator
          screenOptions={{
            headerShown: false,
          }}
        >
          <Tab.Screen name="Translate">
            {props => (
              <TranslateScreen
                {...props}
                favorites={favorites}
                setFavorites={setFavorites}
                history={history}
                setHistory={setHistory}
              />
            )}
          </Tab.Screen>
          <Tab.Screen name="Favorites">
            {props => <FavoritesScreen {...props} favorites={favorites} />}
          </Tab.Screen>
          <Tab.Screen name="History">
            {props => <HistoryScreen {...props} history={history} />}
          </Tab.Screen>
        </Tab.Navigator>
      </NavigationContainer>
    </PaperProvider>
  );
}import React, { useState } from 'react';
import { View, Text, TextInput, StyleSheet, ScrollView, TouchableOpacity } from 'react-native';
import { Card, Button } from 'react-native-paper';

export default function TranslateScreen({ favorites, setFavorites, history, setHistory }) {
  const [text, setText] = useState('');
  const [translated, setTranslated] = useState('');
  const [fromLang, setFromLang] = useState('English');
  const [toLang, setToLang] = useState('Spanish');

  const translateText = () => {
    // Mock translation
    const result = `${text} in ${toLang}`;
    setTranslated(result);

    // Add to history
    setHistory([{ text, translated: result, timestamp: new Date() }, ...history]);
  };

  const saveFavorite = () => {
    setFavorites([{ text, translated, fromLang, toLang }, ...favorites]);
  };

  return (
    <ScrollView style={styles.container}>
      <Text style={styles.label}>Enter text:</Text>
      <TextInput
        style={styles.input}
        placeholder="Type something..."
        value={text}
        onChangeText={setText}
      />

      <Text style={styles.label}>From:</Text>
      <TextInput style={styles.input} value={fromLang} onChangeText={setFromLang} />

      <Text style={styles.label}>To:</Text>
      <TextInput style={styles.input} value={toLang} onChangeText={setToLang} />

      <Button mode="contained" onPress={translateText} style={styles.button}>
        Translate
      </Button>

      {translated ? (
        <Card style={styles.card}>
          <Text style={styles.result}>{translated}</Text>
          <Button mode="outlined" onPress={saveFavorite}>
            Save to Favorites
          </Button>
        </Card>
      ) : null}
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 16, backgroundColor: '#FFFFFF' },
  label: { fontSize: 16, marginTop: 12, color: '#1F2937' },
  input: {
    borderWidth: 1,
    borderColor: '#CBD5E1',
    padding: 12,
    borderRadius: 12,
    marginTop: 6,
    marginBottom: 12,
  },
  button: { marginVertical: 12, backgroundColor: '#3B82F6' },
  card: { padding: 16, borderRadius: 12, backgroundColor: '#F8FAFC', marginTop: 12 },
  result: { fontSize: 16, color: '#1F2937', marginBottom: 8 },
});import React from 'react';
import { ScrollView, Text, StyleSheet } from 'react-native';
import { Card } from 'react-native-paper';

export default function FavoritesScreen({ favorites }) {
  return (
    <ScrollView style={styles.container}>
      {favorites.length === 0 && <Text>No favorites yet.</Text>}
      {favorites.map((item, index) => (
        <Card style={styles.card} key={index}>
          <Text style={styles.text}>
            {item.text} → {item.translated} ({item.fromLang} → {item.toLang})
          </Text>
        </Card>
      ))}
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 16, backgroundColor: '#FFFFFF' },
  card: { padding: 12, marginBottom: 8, borderRadius: 12, backgroundColor: '#F8FAFC' },
  text: { fontSize: 16, color: '#1F2937' },
});import React from 'react';
import { ScrollView, Text, StyleSheet } from 'react-native';
import { Card } from 'react-native-paper';

export default function HistoryScreen({ history }) {
  return (
    <ScrollView style={styles.container}>
      {history.length === 0 && <Text>No history yet.</Text>}
      {history.map((item, index) => (
        <Card style={styles.card} key={index}>
          <Text style={styles.text}>
            {item.text} → {item.translated}
          </Text>
          <Text style={styles.timestamp}>
            {item.timestamp.toLocaleString()}
          </Text>
        </Card>
      ))}
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 16, backgroundColor: '#FFFFFF' },
  card: { padding: 12, marginBottom: 8, borderRadius: 12, backgroundColor: '#F8FAFC' },
  text: { fontSize: 16, color: '#1F2937' },
  timestamp: { fontSize: 12, color: '#6B7280', marginTop: 4 },
})
