# 🎯 Customer Clustering for Marketing Campaign Activation

> Segmentación de clientes mediante técnicas de clustering no supervisado para identificar perfiles accionables y activar campañas de marketing personalizadas.

## 📋 Tabla de Contenidos

- [Contexto de Negocio](#-contexto-de-negocio)
- [Objetivo](#-objetivo)
- [Dataset](#-dataset)
- [Metodología](#-metodología)
- [Resultados Clave](#-resultados-clave)
- [Perfiles de Clientes](#-perfiles-de-clientes)
- [Accionables de Marketing](#-accionables-de-marketing)
- [Stack Técnico](#-stack-técnico)
- [Estructura del Repositorio](#-estructura-del-repositorio)
- [Cómo Ejecutar](#-cómo-ejecutar)
- [Próximos Pasos](#-próximos-pasos)
- [Contacto](#-contacto)

---

## 💼 Contexto de Negocio

Las campañas de marketing genéricas tienen tasas de conversión cada vez más bajas. Un enfoque de **"one-size-fits-all"** desperdicia presupuesto en clientes que no responden y pierde oportunidades con los que sí lo harían con el mensaje adecuado.

Este proyecto aborda ese problema segmentando la base de clientes en grupos con comportamientos y perfiles similares, permitiendo al equipo de marketing **diseñar campañas diferenciadas** que maximicen el ROI y la retención.

---

## 🎯 Objetivo

Construir una segmentación de clientes robusta y accionable que permita:

1. **Identificar perfiles distintos** de clientes basados en su comportamiento de compra, demografía y respuesta a campañas.
2. **Priorizar segmentos** según su valor para el negocio.
3. **Recomendar estrategias de marketing diferenciadas** para cada segmento.

---

## 📊 Dataset

**Fuente:** [Customer Personality Analysis - Kaggle](https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis)

| Dimensión | Detalle |
|---|---|
| **Registros** | ~2,240 clientes |
| **Features originales** | 29 variables |
| **Período** | Datos transaccionales históricos |

Las variables cubren cuatro ejes principales:

- **Demografía:** edad, educación, estado civil, ingresos, hijos
- **Comportamiento de compra:** gasto por categoría (vinos, carnes, frutas, dulces, pescado, oro), compras por canal (web, catálogo, tienda)
- **Respuesta a campañas:** aceptación de 5 campañas de marketing previas
- **Engagement:** recencia de compra, visitas web, quejas

---

## 🔬 Metodología

### 1. Preprocesamiento y Feature Engineering

- **Encoding de variables categóricas:** ordinal encoding para educación (Basic→PhD) y simplificación de estado civil a variable binaria (con/sin pareja).
- **Creación de features agregadas** orientadas a negocio: `TotalSpent`, `TotalPurchases`, `TotalChildren`, `TotalCmpAccepted`, `Age`, `Customer_antiguedad`.
- **Eliminación de variables sin señal:** ID, constantes (`Z_CostContact`, `Z_Revenue`), y registros con edades absurdas (>100 años).
- **Tratamiento de nulos:** eliminación de registros con `Income` nulo (~1% de los datos) dado el bajo impacto en el volumen total.
- **Detección de outliers** con método IQR — se conservan outliers con sentido de negocio (clientes high-value) y se eliminan valores absurdos (`Income > 200K`).

### 2. Escalado

Se utilizó **RobustScaler** (basado en mediana e IQR) en lugar de StandardScaler o MinMaxScaler, dado que se desea conservar la señal de clientes con gasto extremo sin que distorsionen el escalado. Las variables binarias y ordinales no fueron escaladas para respetar su contribución natural a la distancia.

### 3. Reducción de Dimensionalidad

**PCA** sobre las 12 features de entrada. Se seleccionaron **6 componentes principales** que explican ~90% de la varianza acumulada, reduciendo ruido y el efecto de la maldición de la dimensionalidad.

### 4. Clustering — Comparación de 4 Algoritmos

| Algoritmo | Parámetros | Clusters | Silhouette Score |
|---|---|---|---|
| **K-Means** | k=4 | 4 | Mejor balance general |
| Agglomerative (Ward) | k=4 | 4 | Similar a K-Means |
| DBSCAN | eps=1.21, min_samples=13 | 3 + noise | Penalizado por noise points |
| GMM | n_components seleccionado por BIC/AIC | Variable | Asignación probabilística |

**Selección del K óptimo (K-Means):** convergencia de tres métricas — método del codo (inercia), Silhouette Score y Calinski-Harabasz Index — apuntando consistentemente a **k=4**.

**Modelo seleccionado:** K-Means con k=4 por su interpretabilidad, estabilidad y mejor balance de métricas para este caso de uso.

---

## 🏆 Resultados Clave

Se identificaron **4 segmentos de clientes claramente diferenciados**, validados mediante:

- Silhouette Score y Calinski-Harabasz Score
- Pairplots y scatterplots en el espacio PCA
- Boxplots comparativos por variable
- Radar chart para visualización de perfiles
- Tabla de perfilación con medianas por cluster

---

## 👥 Perfiles de Clientes

### Cluster 0 — Premium Loyal 💎

> Alto ingreso, alto gasto, muchas compras, mayor aceptación de campañas, alta antigüedad, pocos hijos.

Son los clientes más valiosos: gastan mucho, responden al marketing y llevan tiempo con la marca.

### Cluster 1 — Mid-Value Browsers 🔍

> Ingreso medio-alto, gasto moderado, buena frecuencia de visitas web, baja respuesta a campañas.

Clientes con potencial de conversión que navegan pero no terminan de comprometerse.

### Cluster 2 — Young Low-Spenders 🌱

> Más jóvenes, ingreso y gasto bajo, baja frecuencia de compra, poca respuesta a campañas.

Segmento en etapa temprana del ciclo de vida del cliente, con oportunidad de desarrollo a largo plazo.

### Cluster 3 — Budget Families 👨‍👩‍👧‍👦

> Ingreso bajo-medio, muchos hijos, gasto concentrado en categorías esenciales, sensibilidad al precio.

Familias que compran lo necesario y responden mejor a descuentos y promociones.

---

## 🚀 Accionables de Marketing

| Segmento | Estrategia | Tácticas Sugeridas |
|---|---|---|
| **Premium Loyal** | Retención y upselling | Programa VIP, acceso anticipado, experiencias exclusivas, cross-selling premium |
| **Mid-Value Browsers** | Reactivación | Win-back campaigns, incentivos por primera compra recurrente, remarketing personalizado |
| **Young Low-Spenders** | Nurturing | Onboarding sequences, contenido educativo, cross-selling de categorías nuevas, engagement digital |
| **Budget Families** | Conversión por volumen | Bundles familiares, descuentos por cantidad, promociones estacionales, programas de lealtad por puntos |

---

## 🛠 Stack Técnico

```
Python 3.x
├── pandas, numpy          → Manipulación de datos
├── scikit-learn           → Clustering (KMeans, Agglomerative, DBSCAN), PCA, preprocessing
├── sklearn.mixture        → Gaussian Mixture Models
├── scipy                  → Dendrogramas (hierarchical clustering)
├── matplotlib, seaborn    → Visualización
└── sklearn.neighbors      → Optimización de hiperparámetros DBSCAN (K-Distance)
```

---

## 📁 Estructura del Repositorio

```
customer-clustering-marketing/
│
├── data/
│   └── marketing_campaign.csv        # Dataset original
│
├── notebooks/
│   └── Customer Personality Analysis.ipynb  # Análisis completo end-to-end
│
├── requirements.txt
├── LICENSE
└── README.md
```

---

## ⚡ Cómo Ejecutar

```bash
# 1. Clonar el repositorio
git clone https://github.com/tu-usuario/customer-clustering-marketing.git
cd customer-clustering-marketing

# 2. Crear entorno virtual
python -m venv venv
source venv/bin/activate  # Linux/Mac
# venv\Scripts\activate   # Windows

# 3. Instalar dependencias
pip install -r requirements.txt

# 4. Ejecutar el notebook
jupyter notebook notebooks/Customer\ Personality\ Analysis.ipynb
```

### Requirements

```
pandas>=1.5
numpy>=1.23
scikit-learn>=1.2
scipy>=1.10
matplotlib>=3.6
seaborn>=0.12
jupyter>=1.0
```

---

## 🔮 Próximos Pasos

- [ ] **Pipeline automatizado:** Refactorizar el notebook a scripts modulares con funciones reutilizables.
- [ ] **Validación de estabilidad:** Bootstrap y análisis de sensibilidad para verificar robustez de los clusters.
- [ ] **CLV por segmento:** Integrar Customer Lifetime Value para priorización basada en valor futuro.
- [ ] **Dashboard interactivo:** Streamlit/Dash para exploración de segmentos por el equipo de marketing.
- [ ] **Monitoreo de drift:** Tracking de cómo los segmentos evolucionan con datos nuevos.


