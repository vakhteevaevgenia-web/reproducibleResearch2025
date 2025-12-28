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
  
  theme(strip.text = element_text(face="italic")) + #курсив
  
geom_pwc(method = "wilcox_test", label="p.adj") #добавление статистических тестов

my_graph #вывод графика

ggsave("CS_with_stats.png", device=png, width=16, height=12, units="cm") #сохранение в файл

![Gammarus lacustris](https://github.com/vakhteevaevgenia-web/reproducibleResearch2025/blob/main/CS%20activity.png)

### НАЗВАНИЕ

setwd("C:\\Users\\vakht\\OneDrive\\Desktop\\Учёба\\Воспроизводимые\\1211")

library(openxlsx)

library(ggplot2)

library(ggpubr)

Jakob <- read.xlsx("Jakob-etal_2021.xlsx", sheet = 3, startRow = 2)

temp_colors_orange <- c("6" = "#ffebcc",
                        "12.4" = "#ff9966",
                        "18.8" = "#cc3300",
                        "23.6" = "#991f00") # Определяем цвета для каждой температуры (4 значений)
                        
ggplot(Jakob, 
       aes(x = factor(`Incubation.temperature.(°C)`), 
       
           y = `CS.activity.(U.mg.(FW)-1)`,
           
           fill = factor(`Incubation.temperature.(°C)`))) +  # Добавляем fill!
           
  geom_boxplot() +
  
  scale_fill_manual(values = temp_colors_orange) +  # Применяем цвета
  
  geom_pwc(method = "wilcox_test", 
  
           label = "{p.adj}, {p.adj.signif}", 
           
           p.adjust.method = "holm", 
           
           ref.group = 1) +
           
  labs(title = "Активность CS при разных температурах инкубации у G. lacustris",
  
       x = "Температура инкубации (°C)", 
       
       y = "Активность CS.(U·mg⁻¹ FW)",
       
       fill = "Температура") +  # Название легенды
       
  theme_minimal() +
  
  theme(legend.position = "right")  # Легенда справа`
  
ggsave(filename="CS_activity.png", device=png, width=18, height=12, units="cm", dpi=300) # сохраняем полученный результат

## общий график

# задаем рабочую директорию (где находятся файлы для анализа)

setwd("C:\\Users\\vakht\\OneDrive\\Desktop\\Учёба\\Воспроизводимые\\1211")

library(openxlsx)

library(ggplot2)

library(ggpubr)

library(tidyr)

# загружаем данные для анализа

Jakob <- read.xlsx("Jakob-etal_2021.xlsx", sheet = 3, startRow = 2)

# Преобразуем данные из широкого формата в длинный
Jakob_long <- pivot_longer(

  Jakob,
  
  cols = c(`LDH.activity.(U.mg.(FW)-1)`, `CS.activity.(U.mg.(FW)-1)`, 
  
           `COX.activity.(U.mg.(FW)-1)`, `PK.activity.(U.mg.(FW)-1)`),# выбираем столбцы для преобразования
           
  names_to = "Enzyme", # задаем имя нового столбца для названия ферментов
  
  values_to = "Activity" # задаем имя нового столбца для активности ферментов
  
)

# Переименовываем ферменты для удобства (через преобразования текста в фактор)

Jakob_long$Enzyme <- factor(Jakob_long$Enzyme, 

                            levels = c("LDH.activity.(U.mg.(FW)-1)", "CS.activity.(U.mg.(FW)-1)",
                            
                                       "COX.activity.(U.mg.(FW)-1)", "PK.activity.(U.mg.(FW)-1)"), # старые названия
                                       
                            labels = c("LDH", "CS", "COX", "PK") # новые названия в том же порядке, что и старые
                            
)

# Создаем цветовую палитру для ферментов

enzyme_colors <- c(

  "LDH" = "#E41A1C",
  
  "CS" = "#377EB8",
  
  "COX" = "#4DAF4A",
  
  "PK" = "#984EA3"
  
)

# Строим график

plot_1 <- ggplot(Jakob_long, 

                 aes(x = `Incubation.temperature.(°C)`, # задаем ось x
                 
                     y = Activity, # задаем ось y
                     
                     color = Enzyme, # цвет соответствует ферменту
                     
                     group = Enzyme)) + # группировка для построения линий
                     
  geom_point(alpha = 0.4, size = 1.5) + # задаем уровень прозрачности и размер точек
  
  # Создаем линии по средним значениям
  
  stat_summary(
  
    fun = mean, # вычисляем среднее
    
    geom = "line", # выбираем геометрию: линию
    
    linewidth=1.2, # толщина линии
    
    aes(group = Enzyme) # группировка по ферменту
    
  ) +
  
  # Вычисляем дов. интервалы (95)
  
  stat_summary(
  
    fun.data = mean_cl_normal,# среднее + 95% ДИ (норм. распред)
    
    geom = "ribbon", # выбираем геометрию: ленту
    
    aes(fill = Enzyme), # заливка по ферменту
    
    alpha = 0.2, #прозрачность
    
    color = NA #без границ
    
  ) +
  
  # Настройка цветов
  scale_color_manual(values = enzyme_colors) + #применяем ранее созданную палитру enzyme_colors к линиям
  
  scale_fill_manual(values = enzyme_colors) + #применяем ранее созданную палитру enzyme_colors к ДИ
  
  # Названия осей и легенды
  
  labs(
  
    title = "Термальные нормы реакции ключевых метаболических ферментов у E. verrucosus", # название графика
    
    x = "Температура инкубации (°C)", # название оси x
    
    y = "Активность фермента (U·mg⁻¹ FW)", #название оси y
    
    color = "Фермент",
    
    fill = "Фермент"
    
  ) +
  
  theme_minimal(base_size = 10) + # базовый размер шрифта 10
  
  theme(
  
    legend.position = "bottom", # положение легенды
    
    plot.title = element_text(face = "bold", hjust = 0.5) # жирный заголовок по центру
    
  )  
  
plot_1

![Gammarus lacustris](https://github.com/vakhteevaevgenia-web/reproducibleResearch2025/blob/main/Act.ferm.png)

## Анализ дифференциальной экспрессии генов

### Скачивание данных с удалённого сервера

export PATH=$PATH:/media/secondary/apps/trinityrnaseq-v2.14.0/util

abundance_estimates_to_matrix.pl --est_method salmon --gene_trans_map none \--name_sample_by_basedir --out_prefix Eve --cross_sample_norm none \Eve*/quant.sf

### Анализ в R studio

