

from aiogram.fsm.context import FSMContext
from aiogram.fsm.state import State,StatesGroup
import logging
import asyncio
from aiogram import Bot,Dispatcher,types,F
from aiogram.filters import Command


api = '7855597648:AAErdPu6O5cGC0LZLZ0WpTsVOtVnGWgfTIg'
bot = Bot(api)
dp=Dispatcher()

class UserState(StatesGroup):
    name = State()
    photo = State()

@dp.message(Command('start'))
async def send_hi(sms:types.Message):
    await sms.answer(text='salem bizin botimizga xosh keldiniz')

@dp.message(Command('help'))
async def send_hi(sms:types.Message):
    await sms.answer(text='sizge jardemim  kerekpe')

@dp.message(Command('info'))
async def send_hi(sms:types.Message):
    await sms.answer(text='bul bot sizdin sorawlarinizga juwap beredi')


@dp.message(Command('basla'))
async def start(message: types.Message,state:FSMContext):
    await state.set_state(UserState.name)
    await message.answer('Введите имя')
@dp.message(UserState.name)
async def get_name(sms: types.Message,state: FSMContext):
    name = sms.text
    await state.update_data(name=name)
    await state.set_state(UserState.photo)
    await sms.answer('Отравьте фото')
@dp.message(F.photo,UserState.photo)
async def get_photo(sms: types.Message,state: FSMContext):
    suwret = sms.photo[-1].file_id
    await state.update_data(suwret=suwret)
    data = await state.get_data()
    await sms.answer_photo(photo=data['suwret'],
                           caption=f"Имя: {data['name']}")
async def main():
    await dp.start_polling(bot)


if __name__=='__main__':
    logging.basicConfig(level=logging.INFO)
    asyncio.run(main())
