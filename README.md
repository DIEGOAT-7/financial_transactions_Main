## **Análisis Financiero Integral: Segmentación de Clientes y Detección de Fraude**

| Archivo                   | Descripción                                                                | Qué haremos con él                                                 |
| ------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `transactions_sample.csv` | Muestra de transacciones (fechas, montos, comercios, IDs de tarjeta, etc.) | Análisis de patrones de gasto, flujos de dinero, tipos de comercio |
| `cards_data.csv`          | Información de las tarjetas (tipo, límites, fechas)                        | Relacionar comportamiento con tipos de tarjetas                    |
| `users_data.csv`          | Perfil de los clientes (demografía, estado, etc.)                          | Segmentación de clientes y análisis de comportamiento              |
| `mcc_codes.json`          | Códigos de categorías de comercios                                         | Categorizar transacciones por tipo de gasto                        |
| `train_fraud_labels.json`      |Muestra de transacciones fraudulentas                     |   Modelo de Detección de Fraude.                           |


## **Resumen del Proyecto**

### **Este es un proyecto integral de ciencia de datos que aborda un dataset financiero desde dos perspectivas críticas: el marketing y la seguridad. El objetivo fue ir más allá de un simple análisis para construir un conjunto de herramientas analíticas interconectadas:**

1. Análisis de Comportamiento: ¿Quiénes son nuestros clientes y cómo se comportan?

2. Inteligencia de Negocio: ¿Cómo podemos visualizar estos comportamientos para tomar decisiones?

3. Defensa Predictiva: ¿Podemos construir un modelo que proteja a estos clientes y al negocio del fraude?

El proyecto va desde la limpieza de datos brutos y el análisis exploratorio, pasando por la segmentación no supervisada de clientes (Clustering), hasta la construcción de un dashboard interactivo en Tableau y el entrenamiento y optimización de un modelo de machine learning para la detección de fraude.

### **Herramientas y Tecnologías Utilizadas**

- **Python:** pandas, numpy, matplotlib, seaborn

- **Machine Learning:** scikit-learn (KMeans, LogisticRegression, RandomForest, train_test_split), XGBoost, LightGBM

- **Manejo de Desequilibrio:** imblearn (SMOTE)

- **Visualización y BI:** Tableau Public

- **Entorno de Desarrollo:** Jupyter Notebooks / VS Code

### **Estructura del Proyecto - El análisis se dividió en cuatro fases principales, cada una construyendo sobre la anterior.**

## **Fase 1: Análisis Exploratorio de Datos (EDA)**

El primer paso fue tomar múltiples fuentes de datos (transacciones, usuarios, tarjetas) y consolidarlas en un "expediente" único y limpio.

### **Qué Hicimos:**

- Se cargaron y fusionaron múltiples archivos CSV y JSON (transactions_data.csv, users_data.csv, cards_data.csv).

- Se realizó una limpieza de datos exhaustiva, incluyendo la conversión de tipos de datos (ej. montos en formato string como '$46487' a numérico) y el manejo de valores nulos.

### **Descubrimiento Clave:** **El archivo de etiquetas de fraude (train_fraud_labels.json) tenía un formato no estándar (un diccionario anidado malformado). Se implementaron técnicas de parsing avanzadas (regex) para extraer y decodificar correctamente las etiquetas de fraude (Yes/No), uniéndolas a las transacciones correspondientes.**

## **Fase 2:** Segmentación de Clientes (Clustering KMeans)
Una vez con los datos limpios, el objetivo fue responder: "¿Tenemos diferentes tipos de clientes?".

### **Qué Hicimos:**

- Se aplicó ingeniería de características a nivel de cliente, agregando métricas clave como Total Spent, Yearly Income, Total Debt y Credit Score.

- Se utilizó el Método del Codo (Elbow Method) para determinar el número óptimo de segmentos de clientes, resultando en k=2.

- Se entrenó un modelo de KMeans para agrupar a todos los clientes en estos dos clústeres.

### **Qué Resolvimos (Los Perfiles):** El análisis de los centroides de cada clúster reveló dos perfiles de cliente claros y accionables:

*Cluster 0:* *"Clientes de Alto Valor". Caracterizados por ingresos y gastos significativamente más altos.*

*Cluster 1:* *"Clientes de Consumo Moderado". Con ingresos y gastos más bajos, representando al cliente "promedio".*

- **Insight de Riesgo:** Un hallazgo crucial fue que los clientes del Cluster 1 eran ~50% más propensos a ser víctimas de fraude, un insight que fue fundamental para el modelo de la Fase 4.

## **Fase 3: Dashboard de Análisis de Comportamiento (Tableau)**

El objetivo fue tomar los insights de la segmentación y presentarlos de una forma accesible e interactiva para la toma de decisiones de negocio.

### **Qué Hicimos:**

Se diseñó y construyó un dashboard ejecutivo en Tableau Public centrado en el comportamiento del cliente.

- **KPIs Dinámicos:** Se crearon KPIs que comparan directamente el rendimiento de ambos clústeres (ej. "El Gasto Promedio del Cluster 0 es 2.3x mayor").

- **Métricas de Negocio Avanzadas:** Se calcularon e implementaron métricas clave como:

- **Participación en la Billetera (Wallet Share):** Descubrimos que nuestros clientes son muy leales, gastando entre el 35% y 45% de sus ingresos anuales a través de nuestras tarjetas.

- **Contexto Socioeconómico:** Revelamos que el 97.7% de nuestra base de clientes gana más que el promedio de su zona geográfica, indicando un perfil de cliente "líder en su comunidad".

- **Interactividad Total:** Se implementaron acciones de filtro y navegación avanzadas. Los usuarios pueden:

   - *Hacer clic en un estado del mapa para ver un desglose del "Top 10 Categorías de Gasto" en esa región.*

   - *Hacer clic en un gráfico de categorías para filtrar la tendencia de gasto a lo largo del tiempo.*

   - *Navegar a dashboards de detalle (como el análisis RFM) y regresar al menú principal con botones de navegación intuitivos.*


## **Fase 4: Modelo de Detección de Fraude (Machine Learning)**

Esta fue la fase más compleja, destinada a construir una herramienta de seguridad robusta y realista.

**El Desafío:** El dataset estaba extremadamente desequilibrado (~99.9% transacciones legítimas vs. ~0.1% fraude). Los modelos estándar fallaron (Precisión/Recall de 0%).

### **Qué Hicimos (El Proceso Iterativo):**

### **Ingeniería de Características (La Clave del Éxito):** Nos enfocamos en crear "pistas" que el modelo pudiera usar para detectar comportamientos anómalos. Las más potentes fueron:

- **amount_vs_avg:** Compara el monto de la transacción con el promedio histórico de ese cliente.

- **time_since_last_txn:** Mide el tiempo en segundos desde la última compra (los fraudes suelen ser más rápidos).

- **txn_count_1h, txn_count_6h, txn_count_24h:** Cuenta el número de transacciones en ventanas de tiempo cortas.

- **amount_vs_avg_merchant:** Compara el monto con el promedio de ese cliente en ese comercio específico.

- **Manejo del Desequilibrio (SMOTE):** Probamos varias técnicas. La estrategia ganadora fue el Sobremuestreo (SMOTE), que crea ejemplos sintéticos de fraude en el set de entrenamiento para que el modelo tenga más casos de los que aprender.

- **Selección del Modelo:** Comparamos LogisticRegression, RandomForest, LightGBM y XGBoost. El modelo campeón fue XGBoost entrenado con los datos de SMOTE, ya que ofreció el mejor equilibrio inicial de métricas.

*Optimización y Evaluación Realista:*

Descubrimos que la optimización de hiperparámetros sobre los datos de SMOTE llevaba a un sobreajuste.

Determinamos que el modelo XGBoost (con parámetros por defecto) + SMOTE + Ingeniería de Características Avanzada era el sistema más robusto.

### **Qué Resolvimos (Los Resultados Finales):**

*El modelo final no es una "bola de cristal", sino una herramienta de negocio configurable. Al evaluarlo en un set de prueba realista (desbalanceado):*

- Con el Umbral por Defecto (0.5):

   - Precisión: 13.90%

   - Recall: 62.24%

**Interpretación: Atrapa a 6 de cada 10 estafadores, pero genera muchas falsas alarmas (1 de cada 7 alertas es correcta).**

- Con el Umbral Optimizado (0.86) para F1-Score:

   - Precisión: 43.37%

   - Recall: 36.73%

**Interpretación: Atrapa a ~4 de cada 10 estafadores, pero con una tasa de falsas alarmas mucho más baja (casi 1 de cada 2 alertas es correcta), lo que es mucho más eficiente para un equipo de investigación.**

### **Conclusión Final: Hemos creado un modelo funcional que proporciona al negocio un trade-off claro y configurable entre la seguridad (maximizar el Recall) y la eficiencia operativa (maximizar la Precisión).**
