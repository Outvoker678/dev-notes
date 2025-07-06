Реагировать на команду `/start`

```Python
@dispatcher.message(CommandStart)
```

Реагировать на команду `/текст`

```Python
@dispatcher.message(Command("текст"))
```

Реагировать на текст

```Python
@dispatcher.message(F.text == "текст")  # На конкретный
@dispatcher.message(F.text)  # На любой
```

Реагировать на фото

```Python
@dispatcher.message(F.photo)
```

Реагировать на CallBack кнопку и ответить

```Python
@dispatcher.callback_query(F.data == "имя_callback")
async def cbfunc(callback: CallBackQuery):
    await callback.answer("",  # Вывести уведомление на экран, можно оставить пустым. Использование обязательное
                          show_alert=True)  # Уведомление придётся самостоятельно закрыть
    await callback.message.answer("текст")  # Отправляет сообщение
    await callback.message.edit_text("текст")  # Изменяет сообщение
    await callback.message.edit_caption("текст")  # Изменяет подпись изображения
    await callback.message.edit_media(...)  # Изменяет изображение
```

Отправить сообщение

```Python
await message.answer("текст")
```

Изменить сообщение

```Python
await bot.edit_message_text(
    chat_id=message.chat.id,
    message_id=message.message_id,
    text="текст")
```

Получить file_id картинки которую отправили в наилучшем качестве

```Python
message.photo[-1].file_id
```

Узнать id чата

```Python
message.chat.id
```

Узнать ID, имя, фамилию, никнейм, язык

```Python
message.from_user.  # Id, first_name, last_name, username, language_code
```

Отправить сообщение по id чата

```Python
await bot.send_photo(chat_id=chat_id, photo="картинка")
```

Отправить картинку по file_id

```Python
await message.answer_photo(photo='file_id картинки')
```

Отправить файл картинки

```Python
await message.answer_photo(photo=InputFile("папка/картинка"))
```

Подписать фото

```Python
await message.answer_photo(photo="картинка", caption="текст")
```

Ответить на сообщение

```Python
await message.reply("текст")
```

Форматировать текст сообщения

```Python
await message.answer("текст", parse_mode="HTML")
```

Подключить роутер

```Python
dispatcher.include_router(start_router)
```

Создать клавиатуру

```Python
downkeyboard = ReplyKeyboardMarkup(
    keyboard=[
        [KeyboardButton(text="первая строка")],  # первая строка
        [KeyboardButton(text="первая строка")],
        [KeyboardButton(text="вторая строка")],  # вторая строка
        [KeyboardButton(text="вторая строка")]
    ],
    resize_keyboard=True,  # Уменьшить размер клавиатуры
    input_field_placeholder="текст"  # Поменять текст в строке ввода
)

await message.answer("текст", reply_markup=keyboard)
```

Создать кнопки под сообщением (inline)

```Python
message_button = InlineKeyboardMarkup(inline_keyboard=[
    [InlineKeyboardButton(text="первая строка", url="ссылка")],
    [InlineKeyboardButton(text="первая строка", url="ссылка")],
    [InlineKeyboardButton(text="вторая строка", url="ссылка")],
    [InlineKeyboardButton(text="вторая строка", url="ссылка")]
])
```

Как создать inline кнопки с CallBack

```Python
message_button = InlineKeyboardMarkup(inline_keyboard=[
    [InlineKeyboardButton(text="первая строка", callback_data="имя_callback")]
])
```

Создать builder клавиатуру

```Python
keyboard = ReplyKeyboardBuilder()
keyboard.add(KeyBoardButton(text="текст"))  # Добавить кнопку

builder.row(  # Добавить строку
    KeyboardButton(text="текст"),
    KeyboardButton(text="текст")
)
```

Создать builder кнопки под сообщением

```Python
keyboard.adjust(количество_кнопок_в_ряду)

keyboard.as_markup(  # Собрать клавиатуру с кнопками
    resize_keyboard=True,  # Уменьшить размер клавиатуры
    input_field_placeholder="текст",  # Поменять текст в строке ввода
    one_time_keyboard=True  # Скрыть клавиатуру после использования
)
```

```Python
inlinekeyboard = InlineKeyboardBuilder()
keyboard.add(InlineKeyboardButton(text=car, url="ссылка"))
keyboard.adjust(2).as_markup()
```

Как создать FSM состояние

```Python
from aiogram.fsm.state import StatesGroup, State

class Reg(StatesGroup):  # Создаём группу состояний для регистрации
    name = State()  # Состояние ввода имени
    number = State()  # Состояние ввода номера
```

Как использовать FSM состояние

```Python
from aiogram.fsm.context import FSMContext

@dispatcher.message(Command('reg'))  # Обработчик команды /reg
async def reg_start(message: Message, state: FSMContext):
    await state.set_state(Reg.name)  # Устанавливаем состояние name
    await message.answer("Введите ваше имя:")  # Запрашиваем имя

@dispatcher.message(Reg.name)  # Обработчик состояния name
async def process_name(message: Message, state: FSMContext):
    await state.update_data(name=message.text)  # Сохраняем имя
    await state.set_state(Reg.number)  # Переключаем на состояние number
    await message.answer("Теперь введите ваш номер телефона:")

@dispatcher.message(Reg.number)  # Обработчик состояния number
async def process_number(message: Message, state: FSMContext):
    await state.update_data(number=message.text)  # Сохраняем номер
    data = await state.get_data()  # Получаем все сохраненные данные

    await message.answer(  # Выводим результат
        f"Регистрация завершена!\n"
        f"Имя: {data['name']}\n"
        f"Номер: {data['number']}"
    )
    await state.clear()  # Очищаем состояние
```

Фильтр контакта

```Python
@dispatcher.message(F.contact)
```