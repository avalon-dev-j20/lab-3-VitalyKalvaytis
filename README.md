# Лабораторная работа №3

*Лабораторная работа №3 по курсу "DEV-J20. Программирование на платформе Java. Стандартные пакеты"*

### Цели работы

1. Научится создавать графический интерфейс пользователя средствами технологии Java.
2. Научится выполнять разметку пользовательского интерфейса с использованием менеджеров компоновки.
3. Научиться обрабатывать события элементов управления.
4. Научится использовать библиотечные элементы управления на примере панелей, кнопок, ползунков, меток.
5. Научится позиционировать элементы управления с учётом требований к адаптивности и отзывчивости пользовательского интерфейса.

### Рассматриваемые темы

В рамках настоящей лабораторной работы рассматриваются следующие темы:

1. создание оконного приложения:
   - инициализация окна;
   - открытие окна;
   - основные свойства окна:
   - фактический размер;
   - предпочтительный размер.
2. компоновка:
   - понятие о контейнере;
   - понятие об элементе управления;
   - элемент управления "метка". Класс JLabel .
4. элементы управления:
   - панели;
   - метки;
   - кнопки;
   - ползунки.
5. события:
   - модель обработки событий в Java;
   - интерфейс EventListener ;
   - эталон проектирования Adapter;
   - применение Adadpter-а для упрощения обработки событий.
5. менеджеры компоновки:
   - интерфейс LayoutManager ;
   - класс BorderLayout ;
   - класс BoxLayout ;
   - класс FlowLayout ;
   - класс GridLayout .

## Ход работы

В рамках лабораторной работы требуется разработать три приложения. Интерфейс каждого приложения описан прототипом, представляющим собой графическое схематическое изображение окна приложения, с расположенными на нём элементами управления и необходимыми комментариями.

Функционал, который необходимо разработать в рамках лабораторной работы описан в терминах функциональных требований к приложению.

Каждое приложение должно быть выполнено как отдельный проект. Таким образом должны получиться три проекта, размещённые в рамках одного решения. Каждый проект описывает одно приложение.

### Приложение "Цветовая палитра"

Приложение представляет собой инструмент, позволяющий пользователю выбирать цвет, с использованием графического пользовательского интерфейса.

#### Интерфейс приложения 

![Интерфейс приложения "Цветовая палитра"](./UI Prototypes/Color Picker.png)

#### Требования к приложению

>  Элемент управления, представляющий собой прямоугольную область, используемую для вывода цвета, будем называть - **холст**.

1. При открытии приложения кнопка позиционируется по центру окна.
2. Окно приложения поддерживает изменение размеров.
3. Окно приложения поддерживает сворачивание на панель задач.
4. Окно приложения поддерживает разворачивание на весь экран.
5. Расстояние от всех элементов управления (кроме "холста") до правого края окна должно быть постоянным.
6. Расстояния от "холста" до левого, верхнего и нижнего краёв окна должны быть постоянными.
7. При наведении курсора мыши на "холст" должна появляться всплывающая подсказка, содержащая шестнадцатеричный код цвета.
8. При изменении позиций ползунков, цвет "холста" должен изменяться на цвет, соответствующий позициям ползунков.
9. При изменении позиций ползунков, цвет, соответствующий их позициям, должен быть записан в буфер обмена в виде строки, содержащей его шестнадцатеричный код.

#### Взаимодействие с буфером обмена

Для взаимодействия с буфером обмена следует получить ссылку на объект класса `Clipboard`. Получить ссылку на экземпляр данного класса можно с использованием экземпляра класса `Toolkit`. Который, в свою очередь, может быть получен с использованием статического метода `getDefaultToolkit` класса `Toolkit`.

```java
import java.awt.Toolkit;
import java.awt.datatransfer.Clipboard;
...
Toolkit toolkit = Toolkit.getDefaultToolkit();
Clipboard clipboard = toolkit.getSystemClipboard();
```

Для записи строки в буфер обмена следует вызвать метод `SetText()`. 

```java
import java.awt.datatransfer.Clipboard;
import java.awt.datatransfer.StringSelection;
...
String text = "Textual data to copty to the clipboard!";
StringSelection selection = new StringSelection(text);
clipboard.setContents(selection, selection);
```

Пример использования данного класса может иметь следующий вид:

```java
import java.awt.*;
import java.awt.event.*;
import java.awt.datatransfer.*;
import javax.swing.*;

public class ClipboardExample extends JFrame {
	/**
	 * Кнопка инициации копирования текста в буфер обмена. 
	 */
    private JButton button = new JButton("Copy to clipboard");
	/**
	 * Текстовое поле, для ввода данных.
	 */
    private JTextField textField = new JTextField("Text to copy to clipboard ...");
	/**
	 * Объект, для взаимодействия с буфером обмена.
	 */
    private Clipboard clipboard = Toolkit
            .getDefaultToolkit()
            .getSystemClipboard();

    public ClipboardExample() {
        // настраиваем окно
        setDefaultCloseOperation(DISPOSE_ON_CLOSE);
        setLocationByPlatform(true);
        setTitle("Clipboard interoperability example");
        // добавляем элементы управления
        add(button, BorderLayout.LINE_END);
        add(textField);
        // добавляем обработчик нажатия на кнопку
        button.addActionListener(this::onButtonClick);
        // упаковываем интерфейс так, чтобы всё поместилось на экране
        pack();
    }
	/**
	 * Выполняет копирование тектса в буфер обмена.
	 *
	 * @param text текстовые данные, которые следует поместить в буфер обмена
	 */
    private void copyToClipboard(String text) {
        StringSelection selection = new StringSelection(text);
        clipboard.setContents(selection, selection);
    }
	/**
     * Проверяет, что строка не пустая.
     *
     * @param text текстовые данные
     * @returns ture, если переданная строка null, не содержит символов, или 
     * 			содержит только пробельные символы. В обратном случае - false.  
     */
    private boolean isBlank(String text) {
        return text == null || text.trim().isEmpty();
    }
	/**
	 * Обработчик события нажатия на кнопку.
	 *
	 * <p> Выполняет копирование текста из текстового поля в буфер обмена, 
	 * если тектс не пустой.
	 *
	 * @param e аргуметны события
	 */
    private void onButtonClick(ActionEvent e) {
        String text = textField.getText();
        if (!isBlank(text)) copyToClipboard(text);
    }
}
```

### Приложение "Калькулятор"

Приложение представляет собой простейшую версию калькулятора, выполняющего основные арифметические действия.

#### Интерфейс приложения

![Интерфейс приложения "Калькулятор"](./UI Prototypes/Calculator.png)

#### Требования к приложению

> Элемент управления, используемый для отображения вводимых пользователем данных и промежуточных результатов вычислений будем называть **дисплеем** приложения.

1. Приложение должно выполнять сложение чисел.
2. Приложение должно выполнять вычитание чисел.
3. Приложение должно выполнять умножение чисел.
4. Приложение должно выполнять деление чисел.
5. Приложение должно позволять пользователю вводить число с использованием кнопок графического интерфейса пользователя.
6. Приложение должно позволять пользователю вводить число с использованием кнопок клавиатуры.
7. Окно приложения поддерживает сворачиванием на панель задач.
8. Окно приложения не поддерживает разворачивание на весь экран.
9. Окно приложения поддерживает изменение размеров.
10. Окно приложения не позволяет изменять размеры окна более заданного максимального размера.
11. Окно приложения не позволяет изменять размеры окна менее заданного минимального размера.
12. Вводимые пользователем данные отображаются на дисплее приложения.
13. Промежуточные результаты вычислений отображаются на дисплее приложения.
14. Расстояние от дисплея до левого, верхнего и правого края клиентской части пользовательского интерфейса должно быть постоянным.
15. При изменении размеров окна, позиции кнопок и их размеры изменяются пропорционально направлению изменения размера окна.
16. Промежуточные результаты вычислений сохраняются в буфер обмена в виде текста.