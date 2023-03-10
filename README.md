# optical_character_recognition
Решается задача optical character recognition (OCR), а именно распознавания номера машины по его изображению.
---
## Данные  

Для обучения и теста использовались изобржаения из корпуса CCPD2019 (https://disk.yandex.ru/d/adjYzzNayB1pag). Все изображения были приведены к размеру (128, 256). Длина каждого номера машины равна 7, всего в номерах встречается 66 различных символов.

## Архитектура
Архитектура достаточно простая. Сначала изображение отправляется в блок resnet без последних двух слоев (resnet-18 БЕЗ предобученных весов), на выходе получим тензор (BS, 512, 4, 8), далее происходит усреднение по высоте и на выходе будет (BS, 512, 8). На этом этапе получены эмбеддинги размерности 512, описывающие изображение (если поделить изображение на 8 равных вертикальных прямоугольников, каждый эмбеддинг будет содержать в себе данные о соответствующем прямоугольнике). После feature extraction подаем эти вектора в Conv1d(512, 66, 2), который выдает тензор (BS, 66, 7), и теперь каждый из 7 векторов будет описывать распределение вероятностей (после применения Softmax) на 66 классов, после использования argmax получим (BS, 7) - тензор итоговых предсказаний модели.
Обучаем модель используя CrossEntropyLoss (усредняя loss-ы по каждому из 7 выходных векторов). Обучаем 10 эпох используя Adam со стандартными параметрами и lr = 0.001.
## Метрики
<table>
  <tr>
    <td></td>
    <th scope="col">ACC</th>
    <th scope="col">CER</th>
  </tr>
  <tr>
    <th scope="row">TRAIN</th>
    <td>0.99719</td>
    <td>0.00044</td>
  </tr>
  <tr>
    <th scope="row">VAL</th>
    <td>0.99706</td>
    <td>0.00044</td>
  </tr>
    <tr>
    <th scope="row">TEST</th>
    <td>0.98280</td>
    <td>0.0028</td>
  </tr>
</table>
