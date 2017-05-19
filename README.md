#README
## Delimitación de especies *Beaucarnea inermis* y *Beaucarnea recurvata*
Este proyecto pretende aclarar la delimitación de dos de las especies que se han considerado *Beaucarnea recurvata*, utilizando datos de secuenciación masiva (GBS). 

Se utilizaron **73204** SNPs de **100** individuos.

### Instalación
-----------------------
Para este proyecto se utilizaron la instalación de los siguientes programas:

**1.** vcftools
**2.** Plink
**3.** Admixture
**4.** FastStructure
**5.** R
-  ggplot2
-  ape
-  ade4
-  Biocondictor
-  SNPRelate
**continuará...** 

### Como usar
---------------------

Este repositorio contiene todos los elementos necesarios para reproducir los análisis mencionados. 

Los datos, *scripts*, resultados y figuras se encuntran dentro de este directorio y en directorios secundarios, los cuales se mencionaran en los diferentes apartados de este README.

**Data**
**Bin** 
**Out**
**Meta**
**Figures**


En el directorio  `proyecto_final/Data` esta contenido el archivo `Beaucarnea_recurvata.MCR50.snps.vcf`, que fue utilizado como base para estos análisis. 

En el directorio `proyecto_final/Bin` se encuentran todos los *scripts* que se corrieron, en el siguiente orden:

**1.** `~/TransformVCF_Plink.sh`
**2.** `~/Admixture/AdmixSeveralK_y_CV.sh`
**3.** `~/Admixture/Cross-Validation.R`
**4.** `~/Admixture/PlotAdmix.R`
**5.** `~/FastStructure/FastStrseveralK.sh`
**6.** `~/FastStructure/PlotStr.R`
**7.** `~/sNMF/Transform vcf_a_genofile`
**7.** `~/sNMF/sNMF_Loop_y_CE.sh`
**continuará...**

### Flujo del análisis
----------------------------------
#### I. Transformación de archivos
##### Para los programas ADMIXTURE y FastStrucuture
El archivo base para los análisis, se encuentra en `~/Data/Beaucarnea_recurvata.MCR50.snps.vcf`, por lo tanto, es necesario transformarlo a archivos con otras caracteristicas y que sean compatibles con los diferentes *softwares* a utilizar.

El archivo **.vcf** se tranforma a archivos **plink** por medio de la linea de comandos en `vcftools`, 
```
vcftools --vcf Beaucarnea_recurvata.MCR50.snps.vcf --plink --out Beaucarnea_recurvata.MCR50.snps
```
**NOTA:** Esta linea de comandos transforma al paquete de archivos **plink** binario (**.ped**, **.map** y **.log**).

Para transformar a los demás archivos de la familia **plink** (**.bed**, **.fam** y **.bim**) se necesita la siguiente linea de comando en el programa **plink**.

```
plink --file  BeauMCR50t1.plk.ped --make-bed --out PBinaryBeauMCR50
```
##### Para el software sNMF
Para poder hacer análisis de ancestría en el programa **sNMF**, es necesario tener un archivo de los genotipos, por lo tanto es necesario transformar el archivo **.vcf** o el archivo **.ped** de la familia **plink**.

En este caso se genero el archivo **.geno** a partir del **.vcf**, con la siguiente linea de comando (`~/sNMF/Transform vcf_a_genofile`):

```
./bin/vcf2geno Beaucarnea_recurvata.MCR50.snps.vcf BeaucarneaMCR50All 
```
**NOTA:** 
- Para ejecutar esta linea es necesrio estar dentro de la carpeta **sNMF** 
- De la ejecución de este comando se generan tres archivos un **.vcf.snp**, **.removed** y otro que no muestra una extensión particular, es necesario agregarle al final la extensión **.geno** para que pueda correr en el programa **sNMF**. 

#### II.  Análisis de Admixture
El análisis de **ADMIXTURE** se utilizo tomando en cuenta el archivo **plink** con la extensión **.bed**. 

- Se corrio un *script* en la Terminal de Unix, el cual contiene un *for loop* para realizar en una sóla corria el análisis par varias agrupaciones (**Ks**), y una linea de comando para obtener la validación cruzada para cada una de las **Ks**.

```
for K in 1 2 3 4 5 6 7;
do ./admixture --cv=7 BeauMCR50.ALL.bed $K | tee log${K}.out; done
grep -h CV log*.out > /BeaucMCR50.ALL.txt

```
**NOTA:** El archivo ejecutable es `~/Admixture/AdmixSeveralK_y_CV.sh`




#### III. Análisis de FastStructure 
El programa **FastStructure** se corrio utilizando los archivos plink contenidos en `~/Data/Plink_MCR50`, estos archivos deben de estar en la carpeta de **FastStructure**.

- Se corrio un *script* en lenguaje **phyton** ( `~/Bin/FastStructure/FastStrseveralK.sh`) que contiene un *For Loop* para realizar en una sola corrida el análisis de una a siete **Ks**.

```
for K in 1 2 3 4 5;
do python structure.py -K $K --input=PBBeau --output=FStrB --full --seed=100; 
done
```
- Se obtienen los archivos **.varP**, **.varQ**,**.meanQ**, **.meanP** y **.log** para cada una de las **Ks** analizadas. 

- Para conocer la **K o Ks** más porbable, se realiza un análisis de elección de **K**, por medio de `chooseK.py`
```
python chooseK.py --input=FStrB
```
**NOTA:**  A diferencia de **ADMIXTURE**, en **FastStructure** la **K** más probable es dada en intervalos como se muestra en el ejemplo siguiente:

```
Model complexity that maximizes marginal likelihood = 5
Model components used to explain structure in data = 3
```
#### IV. Análisis sNMF

#### V.  Plots
##### ADMIXTURE
- Para observar cual de las **Ks** explica mejor nuestro modelo, se grafica en **R** el archivo `~/out/Admixture/BeaucMCR50All.txt`, con el siguiente *script* `~/Bin/Admixture/Cross-Validation.R`. 

- Después, las **K o Ks**  que mejor explican se grafican en **R**  siguiendo el *script* `~/Admixture/PlotAdmix.R`. 

##### FastStructure
- Después, las **K o Ks**  que mejor explican se grafican en **R**  siguiendo el *script* `~/FastStructure/PlotStr.R`. 

##### sNMF

#### VI. Árbol filogenético 

**continuará...**

### Creditos

**Elaborado por:** Myriam Campos Aguilar.  IE, UNAM.
