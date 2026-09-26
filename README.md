# Конспект с Expo

## Структура и организация процессов
**Этапы жизненного цикла:** Вся документация разделена по шагам работы над приложением. Раздел **Develop** содержит инструменты для написания кода и навигации, **Review** отвечает за совместное тестирование, **Deploy** описывает публикацию в магазины, а **Monitor** отслеживает работу сервисов в продакшене.

**Формат для ИИ-агентов:** Текст страниц адаптирован для чтения языковыми моделями (LLM). Если добавить расширение *.md* к любому URL-адресу документации, она откроется в виде чистого Markdown-файла.

## Возможности платформы и SDK
**Универсальный код:** Разработчик пишет один проект на JavaScript/TypeScript, который работает на Android, iOS и в веб-браузерах.

**Файловый роутинг:** Модуль **Expo Router** строит навигацию между экранами приложения автоматически на основе структуры папок и файлов проекта.

**Компоненты SDK:** Платформа предоставляет встроенные модули для доступа к функциям устройств. В качестве примеров в документации выделены модули Image (изображения), **Camera** (камера) и **Notifications** (уведомления).

**Тестирование без установки:** Инструмент **Expo Snack** позволяет запускать и проверять код приложения прямо в браузере без настройки локального окружения.

## Интеграция с искусственным интеллектом (AI)
**Инструменты для агентов:** В документацию добавлен отдельный блок по работе с ИИ. Он содержит руководства по **Expo Skills**, **MCP Server**, готовые наборы инструментов (**Agent toolkits**) и разделы для интеграции с **LLM.**

**Примеры реализации:** На сайте представлены готовые проекты для изучения, среди которых есть пример интеграции **API Routes + Open AI.**

## Консольные команды и автоматизация (CLI)
*npx create-expo-app@latest* — Команда для создания и инициализации нового чистого проекта.

*npx testflight* — Эксклюзивная команда для iOS, которая загружает готовую сборку приложения на платформу TestFlight.

*npx eas-cli deploy* — Инструмент для развертывания и публикации веб-версии вашего приложения.

**Сервис EAS Workflows:** Используется для настройки автоматического CI/CD цикла, позволяя собирать и выпускать релизы напрямую через GitHub.


# Конспект Expo tutorial

## 1. Introduction (Введение)

Цель туториала — познакомиться с Expo SDK и построить приложение, которое:

- создаётся из стандартного шаблона с включённым TypeScript;
- использует **Expo Router** для навигации (стек + нижние табы);
- строит макет экрана с помощью **Flexbox**;
- позволяет выбрать изображение из галереи устройства;
- показывает модальное окно выбора стикера (`<Modal>`, `<FlatList>`);
- обрабатывает жесты (тап, перетаскивание);
- делает скриншот и сохраняет его на устройство;
- учитывает различия между Android, iOS и Web;
- настраивает статус-бар, иконку и заставку (splash screen).



Пример стартового кода ("Hello World"):

```tsx
import { StyleSheet, Text, View } from 'react-native';

export default function Index() {
  return (
    <View style={styles.container}>
      <Text>Hello world!</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

---

## 2. Create your first app (Создание первого приложения)

Для инициализации проекта используется утилита `create-expo-app`. Стандартный шаблон включает:
- пакет `expo` и Expo CLI;
- готовую табовую навигацию на Expo Router;
- автоматическую настройку под Android/iOS/Web;
- предустановленный TypeScript.

Стили в React Native задаются не CSS, а JS-объектами через `StyleSheet.create()`, но многие свойства похожи на CSS (flex, colors и т.д.).

### Основные шаги
1. Создать проект:
```bash
npx create-expo-app@latest StickerSmash
cd StickerSmash
```
2. Скачать и распаковать assets (изображения) в `assets/images`.
3. Выполнить `npm run reset-project` — удаляет шаблонный код, оставляя `index.tsx` и `_layout.tsx`.
4. Запустить сервер разработки:
```bash
npx expo start
```
5. Отредактировать `src/app/index.tsx`:

```tsx
import { Text, View, StyleSheet } from 'react-native';

export default function Index() {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Home screen</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#25292e', alignItems: 'center', justifyContent: 'center' },
  text: { color: '#fff' },
});
```

---

## 3. Add navigation (Добавление навигации)

**Expo Router** — файловая система маршрутизации: каждый файл в директории `app` становится экраном в приложении и страницей в вебе.

Ключевые понятия:
- **app directory** — содержит только маршруты и layout-файлы;
- **Root layout** (`_layout.tsx`) — общий UI (шапки, табы) для всех маршрутов;
- имя файла `index.tsx` соответствует родительскому маршруту (`/`);
- `+not-found.tsx` — экран для несуществующих маршрутов (аналог 404).

### Стек-навигатор
Компонент `<Stack>` создаёт переходы между экранами (анимация сверху на Android, справа на iOS).

```tsx
import { Stack } from 'expo-router';

export default function RootLayout() {
  return (
    <Stack>
      <Stack.Screen name="index" options={{ title: 'Home' }} />
      <Stack.Screen name="about" options={{ title: 'About' }} />
    </Stack>
  );
}
```

### Переход между экранами
Компонент `<Link>` из `expo-router` рендерит `<Text>` со ссылкой `href`:

```tsx
import { Link } from 'expo-router';

<Link href="/about" style={{ color: '#fff' }}>Go to About screen</Link>
```

### Таб-навигатор
Группа `(tabs)` создаёт нижнюю панель вкладок:

```tsx
import { Tabs } from 'expo-router';

export default function TabLayout() {
  return (
    <Tabs screenOptions={{ tabBarActiveTintColor: '#ffd33d' }}>
      <Tabs.Screen name="index" options={{ title: 'Home' }} />
      <Tabs.Screen name="about" options={{ title: 'About' }} />
    </Tabs>
  );
}
```

Иконки табов подключаются из библиотеки `@expo/vector-icons` (например, `Ionicons`) через параметр `tabBarIcon`.

---

## 4. Build a screen (Создание экрана)

Экран разбивается на составные части: изображение по центру и две кнопки внизу. Для показа изображений используется кроссплатформенный компонент `<Image>` из библиотеки `expo-image` — он принимает либо локальный файл (`require(...)`), либо `uri` из сети.

Для интерактивных элементов рекомендуется компонент **`<Pressable>`** — он гибче, чем `<Button>`, и поддерживает разные события нажатия.

### Пример: показ изображения

```tsx
import { Image } from 'expo-image';

const PlaceholderImage = require('@/assets/images/background-image.png');

<Image source={PlaceholderImage} style={{ width: 320, height: 440, borderRadius: 18 }} />
```

### Пример: кнопка на Pressable

```tsx
import { Pressable, Text, View } from 'react-native';

export default function Button({ label }: { label: string }) {
  return (
    <View>
      <Pressable onPress={() => alert('You pressed a button.')}>
        <Text>{label}</Text>
      </Pressable>
    </View>
  );
}
```

Компоненты рекомендуется выносить в отдельные файлы (директория `src/components`), а не хранить всё в `app`, так как `app` предназначена только для маршрутов.

Для стилизации «основной» кнопки используется проп `theme="primary"` и инлайн-стили, которые перекрывают стили из `StyleSheet.create()`, плюс иконка из `@expo/vector-icons` (например `FontAwesome`).

---

## 5. Use an image picker (Выбор изображения)

Для выбора изображения из галереи используется библиотека **`expo-image-picker`** — она открывает системный интерфейс выбора фото/видео.

Установка:
```bash
npx expo install expo-image-picker
```

### Пример

```tsx
import * as ImagePicker from 'expo-image-picker';
import { useState } from 'react';

const [selectedImage, setSelectedImage] = useState<string | undefined>(undefined);

const pickImageAsync = async () => {
  const result = await ImagePicker.launchImageLibraryAsync({
    mediaTypes: ['images'],
    allowsEditing: true,
    quality: 1,
  });

  if (!result.canceled) {
    setSelectedImage(result.assets[0].uri);
  } else {
    alert('You did not select any image.');
  }
};
```

Метод `launchImageLibraryAsync()` возвращает объект с массивом `assets`, в котором лежит `uri` выбранного файла. Если `selectedImage` задан, он используется вместо изображения-заглушки — через тернарный оператор внутри компонента `ImageViewer`.

---

## 6. Create a modal (Создание модального окна)

Компонент React Native **`<Modal>`** показывает контент поверх остального приложения — обычно для привлечения внимания или выбора опции. В туториале создаётся модальное окно выбора эмодзи-стикера.

Ключевые пропсы `<Modal>`:
- `visible` — управляет видимостью;
- `transparent` — занимает ли модалка весь экран;
- `animationType` — способ появления (например, `"slide"`).

### Пример модального окна

```tsx
import { Modal, View, Text, Pressable } from 'react-native';

export default function EmojiPicker({ isVisible, children, onClose }) {
  return (
    <Modal animationType="slide" transparent={true} visible={isVisible}>
      <View>
        <Text>Choose a sticker</Text>
        <Pressable onPress={onClose}>
          <Text>Закрыть</Text>
        </Pressable>
        {children}
      </View>
    </Modal>
  );
}
```

### Список эмодзи
Для горизонтального списка используется **`<FlatList>`** — компонент для эффективного рендеринга списков. Ключевые пропсы: `data` (массив), `renderItem` (функция отрисовки элемента), `horizontal` (горизонтальная прокрутка).

```tsx
<FlatList
  horizontal
  data={emoji}
  renderItem={({ item }) => (
    <Pressable onPress={() => onSelect(item)}>
      <Image source={item} style={{ width: 100, height: 100 }} />
    </Pressable>
  )}
/>
```

Выбранный эмодзи хранится в состоянии (`useState`) и накладывается на изображение через отдельный компонент `EmojiSticker`.

---

## 7. Add gestures (Добавление жестов)

Для обработки жестов используется библиотека **React Native Gesture Handler**, а для анимаций — **Reanimated**. Приложение должно быть обёрнуто в `<GestureHandlerRootView>`.

Два жеста в туториале:
- **двойной тап** — увеличивает/уменьшает размер стикера;
- **pan (перетаскивание)** — двигает стикер по экрану.

`useSharedValue()` создаёт «общее» значение, которое можно менять и на основе которого строится анимация. `useAnimatedStyle()` формирует стиль, реагирующий на изменение shared-значений.

### Пример: жест двойного тапа

```tsx
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useAnimatedStyle, useSharedValue, withSpring } from 'react-native-reanimated';

const scaleImage = useSharedValue(imageSize);

const doubleTap = Gesture.Tap()
  .numberOfTaps(2)
  .onStart(() => {
    scaleImage.value = scaleImage.value !== imageSize * 2
      ? scaleImage.value * 2
      : Math.round(scaleImage.value / 2);
  });

const imageStyle = useAnimatedStyle(() => ({
  width: withSpring(scaleImage.value),
  height: withSpring(scaleImage.value),
}));
```

### Пример: жест перетаскивания (pan)

```tsx
const translateX = useSharedValue(0);
const translateY = useSharedValue(0);

const drag = Gesture.Pan().onChange(event => {
  translateX.value += event.changeX;
  translateY.value += event.changeY;
});

const containerStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: translateX.value }, { translateY: translateY.value }],
}));
```

Оба жеста оборачивают элемент в `<GestureDetector gesture={...}>`.

---

## 8. Take a screenshot (Сохранение скриншота)

Для захвата области экрана используется сторонняя библиотека **`react-native-view-shot`**, а для сохранения в галерею — **`expo-media-library`**.

Установка:
```bash
npx expo install react-native-view-shot expo-media-library
```

Перед сохранением нужно запросить разрешение на доступ к медиатеке через хук `useMediaLibraryPermissions()`.

### Пример

```tsx
import { captureRef } from 'react-native-view-shot';
import * as MediaLibrary from 'expo-media-library';
import { useRef } from 'react';

const imageRef = useRef<View>(null);

const onSaveImageAsync = async () => {
  try {
    const localUri = await captureRef(imageRef, { height: 440, quality: 1 });
    await MediaLibrary.saveToLibraryAsync(localUri);
    alert('Saved!');
  } catch (e) {
    console.log(e);
  }
};
```

Элемент, который нужно захватить, оборачивается в `<View ref={imageRef} collapsable={false}>` — свойство `collapsable={false}` не даёт React Native «схлопнуть» View при оптимизации, что нужно для корректного скриншота.

---

## 9. Handle platform differences (Обработка различий платформ)

Не все библиотеки работают одинаково на Android, iOS и Web. Например, `react-native-view-shot` не работает в браузере — вместо неё для веба используется библиотека **`dom-to-image`**, которая превращает DOM-узел в изображение (SVG/PNG/JPEG).

Определить текущую платформу помогает модуль **`Platform`** из `react-native` — свойство `Platform.OS` возвращает `'ios' | 'android' | 'web'`.

### Пример

```tsx
import { Platform } from 'react-native';
import domtoimage from 'dom-to-image';

const onSaveImageAsync = async () => {
  if (Platform.OS !== 'web') {
    // логика для Android/iOS через captureRef()
  } else {
    const dataUrl = await domtoimage.toJpeg(imageRef.current, { quality: 0.95 });
    const link = document.createElement('a');
    link.download = 'sticker-smash.jpeg';
    link.href = dataUrl;
    link.click();
  }
};
```

Для TypeScript-проекта, использующего `dom-to-image` (нет встроенных типов), создаётся файл `types.d.ts`:

```ts
declare module 'dom-to-image';
```

---

## 10. Configure status bar, splash screen and app icon (Настройка внешнего вида)

Перед публикацией приложения настраиваются три элемента оформления:

1. **Статус-бар** — библиотека `expo-status-bar` (входит в шаблон по умолчанию) даёт компонент `<StatusBar>` для настройки стиля (светлый/тёмный текст).
2. **Иконка приложения** — файл `assets/images/icon.png` (1024×1024 px), путь к которому указан в `app.json` в поле `"icon"`.
3. **Заставка (splash screen)** — настраивается через плагин `expo-splash-screen` в `app.json`; протестировать её можно только в preview/production сборке, не в Expo Go.

### Пример настройки статус-бара

```tsx
import { Stack } from 'expo-router';
import { StatusBar } from 'expo-status-bar';

export default function RootLayout() {
  return (
    <>
      <Stack>
        <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
      </Stack>
      <StatusBar style="light" />
    </>
  );
}
```

Пример конфигурации splash screen в `app.json`:

```json
{
  "plugins": [
    ["expo-splash-screen", { "image": "./assets/images/splash-icon.png" }]
  ]
}
```

---

## 11. Learning resources (Дополнительные материалы)

Итоговая глава туториала собирает ссылки для дальнейшего изучения:

- **Инструменты разработки Expo** — обзор инструментов CLI и Dev Tools.
- **Development builds** — сборки для полного контроля над процессом (в отличие от Expo Go).
- **Expo Router** — подробная документация по файловой маршрутизации.
- **Иконка и заставка приложения**, а также справочник `app.json`.
- **Публикация и дистрибуция** приложения в App Store / Google Play.
- **Отладка (Debugging)** — инструменты поиска и исправления ошибок.

Рекомендованные темы для углублённого изучения:
- **React** — официальная документация (Quick Start, Hooks).
- **React Native** — базовые компоненты (`View`, `Text`), платформенный код, списки, **Flexbox** для вёрстки.
- **Жесты и анимации** — документация React Native Gesture Handler и Reanimated.

Также предлагается вступить в сообщество Expo в Discord для вопросов и общения с другими разработчиками.

