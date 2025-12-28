# reproducibleResearch2025
# Резюме

Gammarus lacustris - голарктическая амфипода, описанна Георгом Осианом Сарсом в 1863 году. Данному виду свойственно: широкий предел экологической толерантности, высокая фенотипическая пластичность и обширный ареал, распространяющийся в пределах Южной Европы и Центральной Азии. Повсеместное распространение включается в себя как глубокие и мелководные озёра, так и реки Голарктики, что демонстрирует значительные сезонные колебания температуры. 

Температура — самый распространённый абиотический фактор для животных, так как влияет на молекулярную динамику и скорость биохимических реакций.  Такой лимитирующий фактор накладывает температурные ограничения, влияющие на рутинный метаболизм и вентиляционную активность. Критическая температура, определяемая ограничениями потребления кислорода, для G. lacustris находится в диапазоне 23–25 °C. 

Данное исследование направлено на изучение нормы тепловой реакции посредством анализа ключевых метаболических процессов, в том числе активность ферментов: цитратсинтаза (CS), цитохром-с-оксидаза (COX), пируваткиназа (PK), лактатдегидрогеназа (LDH), гидроксиацилдегидрогеназа (HADH) и глутаматдегидрогеназа (GDH); а также уровеня дифференциальной экспрессии генов. Условия содержания были приближены к естественным за счёт регулярной смены воды и имитации температурных изменений во время летнего потепления (температуры повышалась на 0,8 °C в день). 

![Gammarus lacustris](https://github.com/vakhteevaevgenia-web/reproducibleResearch2025/blob/main/Airbrush-IMAGE-ENHANCER-1766905686873-1766905686873.png)

Gammarus lacustris

# Методы

## Работа на удалённых серверах - bash

### Основные команды bash 

pwd (print working directory) - показывает полный путь к директории на текущий момент, ваше местоположение сейчас;

cd (change directory) - перемещение в другую директорию. 

Комментарии:
. = начало отнсительного пути
.. = подняться на уровень выше
~ = домашняя папка
/ = разделитель уровней
Tab = автозаполнение 

mkdir (make directory) = создать пустую папку

ls (list) = посмотреть содержимое папки

Комментарии:
h = удобное отображение размера
t = сортировка по размеру
l = отображение таблицы 

head, tail = посмотреть начало и конец файла

cat = посмотреть весь файл

grep = поиск текста в файле

cp (copy) = копировать файл 

Комментарии:
-r = работа с папкой 

mv (move) = переместить или переименовать

rm (remove) = удалить

Комментарии:
-r = удалить папку

screen = запуск процессов в режиме сессии

Комментарии:
screen -s "название" = создать, 
screen -r "название" = вернуться
screen -ls = "название" = показать список
Ctrl+A+D = выйти из процесса

### Скачивание данных

Показываем, где находится исполняемый файл программы:

export PATH=$PATH:/media/secondary/apps/sratoolkit.3.0.0-ubuntu64/bin/

Скачиваем данные (Gammarus lacustris)

fasterq-dump --threads 2 -A --progress SRR8205844 ; fasterq-dump --threads 2 -A --progress SRR8205843 ; fasterq-dump --threads 2 -A --progress SRR8205847 ; fasterq-dump --threads 2 -A --progress SRR8205848 ; fasterq-dump --threads 2 -A --progress SRR8206019 ; fasterq-dump --threads 2 -A --progress SRR8206020 ; fasterq-dump --threads 2 -A --progress SRR8206021

### Вырвнивание чтений на референс

Скачивание референса

wgethttps://ftp.ncbi.nlm.nih.gov/geo/series/GSE129nnn/GSE129069/suppl/GSE129069%5FEveBCdTP1%5Fani%2Efasta%2Egz

Распаковка ахрива

gunzip GSE129069_EveBCdTP1_ani.fasta.gz

Путь к программе

export PATH=$PATH:/media/secondary/apps/trinityrnaseq-v2.14.0/util/

Подготавка референса Salmon

lign_and_estimate_abundance.pl --transcripts GSE129069_EveBCdTP1_ani.fasta --est_method salmon --trinity_mode --prep_reference

Скачивание таблицы образцов

wgethttps://raw.githubusercontent.com/drozdovapb/Reproducible_research/refs/heads/main/Data/Eve_samples.txt

Выравнивание с помощью Salmon

align_and_estimate_abundance.pl --transcripts GSE129069_EveBCdTP1_ani.fasta --seqType fq --samples_file Eve_samples.txt --est_method salmon --trinity_mode --output_dir . --thread_count 2 --SS_lib_type FR

## Построение графиков в R studio

#Прописываем путь к рабочей директории

setwd("C:\\Users\\vakht\\OneDrive\\Desktop\\Учёба\\Воспроизводимые\\1211")

#Подключаем необходимые пакеты

library(ggplot2)

library(ggpubr)

library(openxlsx)

#Загружаем таблицу

tbl <- read.xlsx("Jakob-etal_2021.xlsx", sheet = 4) 

#Проверка данных

str(tbl)

hist(tbl$CS.activity)

hist(tbl$LDH.activity)

#Построение графиков

my_graph <- ggplot(data=tbl, aes(x=Species, y=CS.activity)) +

  expand_limits(y=0) + #y=0 включаем
  
  geom_boxplot(show.legend = FALSE) + #боксплоты (без легенды)
  
  ylab("CS activity, a.u.") + xlab("") + #название оси Y
  
  theme_bw(base_size = 16) + #увеличим размер шрифта + белый фон
  
  theme(strip.text = element_text(face="italic")) #курсив

my_graph #вывод графика

ggsave("CS_with_stats.png", device=png, width=20, height=12, units="cm") #сохранение в файл

![Gammarus lacustris](https://github.com/vakhteevaevgenia-web/reproducibleResearch2025/blob/main/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-12-28%20211317.png)


