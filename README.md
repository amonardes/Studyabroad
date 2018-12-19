# Studyabroad
Data Analysis of Study Abroad department from Brown University

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt


#runfile("/home/andres/PycharmProjects/HelloPyCharm/Intro.py", wdir='/home/andres/PycharmProjects/HelloPyCharm')

data = pd.read_csv("rs/study.csv")
#print(Study.head(5)) IMRIME LAS PRIMERAS FILAS PARA SABER SI SE IMORTO BIEN
#print(Study.dtypes)  IMPRIME TIPO DE DATA PARA SABER QUE NORMALIZAR
#print(Study.["Year"] DETALLES DE LA COLUMNA PARA IDENTIFICAR ERROR, VALORES UNICOS DE LA SERIES (TODOS LOS ANOS)
#print(Study["Country"].unique())   VALORES UNICOS DE LA SERIE (TODOS LOS PAISES)

data["ID"] = list(range(len(data)))

df1 = data[-data["Year"].str.contains(",")]  # DATA NORMALIZADA

df2 = data[data["Year"].str.contains(",")]  #DATA NO NORMALIZADA

# Separacion la serie de anos juntos, por anos separados.
s = df2["Year"].str.replace(" ", "").str.split(",").apply(pd.Series, 1).stack()
# Asegurar referencia a df2.
s.index = s.index.droplevel(-1)
s.name = "Year"
del df2["Year"]
df2 = df2.join(s)
# CREAMOS DATA ORIGINAL TOTALMENTE NORMALIZADA, CONCATENANDO NUESTROS DFs. y convertimos nuestra columna de anos en IN64.
data = pd.concat([df1, df2], sort=False)
data.Year = pd.to_numeric(data.Year, errors='coerce').fillna(0).astype(np.int64)

del df1, df2, s


print(data)


#-------------------------------------------------------------------------------------
# Para separar DF de MALE y FEMALE

dataF = data[data["Gender"] == "Female"]

dataM = data[data["Gender"] == "Male"]

CountriesF = dataF.groupby("Country")["ID"].count()
CountriesM = dataM.groupby("Country")["ID"].count()

#OR
dataF["Country"].value_counts()
dataM["Country"].value_counts()
#-------------------------------------------------------------------------------------

Countries_and_major_F = dataF.groupby(["Country","Major"])["Year"].count()
Countries_and_major_M = dataM.groupby(["Country","Major"])["Year"].count()

#-------------------------------------------------------------------------------------

#Basandonos en una Tabla de Pivoteo, podemos visualizar el numero de estudiantes por anio en cada pais. Ademas de crear una columna de total.
Estudiante_por_pais = data.pivot_table(index='Country', columns='Year', values='ID', aggfunc='count')
Estudiante_por_pais['totals'] = Estudiante_por_pais.sum(axis='columns')
Estudiante_por_pais.fillna(0)

