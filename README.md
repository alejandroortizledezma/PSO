# Optimización de Partículas en Parvada, Particle Swarm Optimitation (PSO) 
> Es una herística para optimizar ecuaciones no lineales que simula el vuelo de una parvada; la cual sigue a un lider que en la heurística es la partícula o pajaro que ha obtenido hasta ese momento la menor evaluación en la función objetivo. Las demás partículas se mueven desde su posición con cierto peso entre su propia mejor evaluación y la del lider, esos pesos pueden ajustarse.


This file will become your README and also the index of your documentation.

## Install

`pip install PSO`

## How to use

Importamos las librerias a utilizar

```
import math
import numpy as np
from functools import reduce
from random import uniform, seed
from statistics import mean, pstdev
```

La clase Ave va a ser cada una de nuestras partículas.

```
class Ave(object):
    def __init__(self, posicion, mejorPosicion, velocidad, aptitud):
        self.posicion= posicion
        self.mejorPosicion= mejorPosicion
        self.velocidad= velocidad
        self.aptitud= aptitud
    def x(self):
        return self.posicion
    def p(self):
        return self.mejorPosicion
    def v(self):
        return self.velocidad
    def a(self):
        return self.aptitud
```

Las funciones g6 y g13, son las funciones a optimizar, fi6 y fi13 son sus respectivas penalizaciones que utilizamos en jerarquización estocástica

```
def g6(xs):
    x1= xs[0]
    x2= xs[1]
    return (x1 - 10)**3 + (x2 - 20)**3
    #sujeto a 
def fi6(xs, verbose= False):
    x1= xs[0]
    x2= xs[1]
    penalizacion= 0
    g1= -(x1 - 5)**2 - (x2 - 5)**2 + 100 #<= 0
    if g1 > 0:
        penalizacion += g1
        if verbose == True:
            print('Penalizacion g1: ',g1)
    elif verbose == True:
        print('Penalizacion g1: 0')
    g2= (x1 - 6)**2 + (x2 - 5)**2 -82.81 #<= 0
    if g2 > 0:
        penalizacion += g2
        if verbose == True:
            print('Penalizacion g2: ',g2)
    elif verbose == True:
        print('Penalizacion g2: 0')
    # donde
    if x1 < 13:
        penalizacion+= 13 - x1
    if x1 > 100:
        penalizacion+= x1 - 100
    if x2 < 0:
        penalizacion+= abs(x2)
    if x2 > 100:
        penalizacion+= x2 - 100
    #13 <= x1 <= 100
    #0 <= x2 <= 100
    return penalizacion
def g13(x):
    n= math.e **(x[0]*x[1]*x[2]*x[3]*x[4])
    return n
    #sujeto a:
def fi13(xs, verbose= False):
    x1= xs[0]
    x2= xs[1]
    x3= xs[2]
    x4= xs[3]
    x5= xs[4]
    penalizacion= 0
    h1= abs(x1**2 + x2**2 + x3**2 + x4**2 + x5**2 -10) #== 0
    penalizacion += h1
    h2= abs(x2 * x3 - 5*x4 * x5) #== 0
    penalizacion += h2
    h3= abs(x1**3 + x2**3 + 1) #== 0
    penalizacion += h3
    if verbose == True:
        print('h1: ',h1)
        print('h2: ',h2)
        print('h3: ',h3)
    # donde
    for x in xs[0:3]:
        if x < -2.3:
            penalizacion+= abs(x + 2.3)
        if x > 2.3:
            penalizacion+= x - 2.3
    for x in xs[3:]:
        if x < -3.2:
            penalizacion+= abs(x + 3.2)
        if x > 3.2:
            penalizacion+= x - 3.2
    #-2.3 <= xi <= 2.3 (i= 1, 2)
    #-3.2 <= xi <= 3.2 (i= 3, 4, 5)
    return penalizacion
def jerarquizacionEstocastica(I, mejor):
    for i in range(len(I)):
        u= uniform(0, 1)
        if (fi(I[i]) == 0 and fi(I[mejor]) == 0) or u < P:
            if f(I[mejor]) > f(I[i]):
                mejor= i
            elif fi(I[mejor]) > fi(I[i]):
                    mejor= i
    return mejor
```

```
P = .45
maximoGeneraciones=100
tamañoPoblacion=200
w=.8
c1=.4
c2=.6
N= tamañoPoblacion
l=N
funcion= input("Favor de ingresar la función a optimizar\n\tg6\tg13\n")
funcion= funcion.lower()
print('Función a optimizar: ', funcion)
fs= []
for s in range(31):
    seed(s)
    print('semilla: ', s)
    if funcion == "g6":
        f= g6
        fi= fi6
        I= []
        mejorGlobal= 0
        for i in range(tamañoPoblacion):
            x1= uniform(13, 100)
            x2= uniform(0, 100)
            individuo= np.array([x1, x2])
            fAve=f(individuo)
            fiAve= fi(individuo)
            aptitud= fAve + fiAve
            ave= Ave(individuo,individuo, 0, [fAve, fiAve])
            I.append(ave)
            u= uniform(0, 1)
            if (fiAve == 0 and I[mejorGlobal].a()[1] == 0) or u < P:
                if I[mejorGlobal].a()[0] > fAve:
                    mejorGlobal= i
                elif I[mejorGlobal].a()[1] > fiAve:
                    mejorGlobal= i
    if funcion == "g13":
        f= g13
        fi= fi13
        I= []
        mejorGlobal= 0
        for i in range(tamañoPoblacion):
            x1= uniform(-2.3, 2.3)
            x2= uniform(-2.3, 2.3)
            x3= uniform(-3.2, 3.2)
            x4= uniform(-3.2, 3.2)
            x5= uniform(-3.2, 3.2)
            individuo= np.array([x1, x2, x3, x4, x5])
            fAve=f(individuo)
            fiAve= fi(individuo)
            aptitud= fAve + fiAve
            ave= Ave(individuo,individuo, 0, [fAve, fiAve])
            I.append(ave)
            u= uniform(0, 1)
            if (fiAve == 0 and I[mejorGlobal].a()[1] == 0) or u < P:
                if I[mejorGlobal].a()[0] > fAve:
                    mejorGlobal= i
                elif I[mejorGlobal].a()[1] > fiAve:
                    mejorGlobal= i
    generacion= 0
    while generacion < maximoGeneraciones:
        for i in range(tamañoPoblacion):
            nuevaVelocidad= w * I[i].v() + c1 * uniform(0, 1) * (I[i].p() - I[i].x()) + c2 * uniform(0, 1) * (I[mejorGlobal].p() - I[i].x())
            I[i].velocidad= nuevaVelocidad
            nuevaPosicion= I[i].x() + nuevaVelocidad
            I[i].posicion= nuevaPosicion
            fAve=f(nuevaPosicion)
            fiAve= fi(nuevaPosicion)
            nuevaAptitud= fAve + fiAve
            if nuevaAptitud < I[i].a()[0] + I[i].a()[1]:
                I[i].mejorPosicion= nuevaPosicion
            I[i].aptitud= [fAve, fiAve]
            u= uniform(0, 1)
            if (fiAve == 0 and I[mejorGlobal].a()[1] == 0) or u < P:
                if I[mejorGlobal].a()[0] > fAve:
                    mejorGlobal= i
                elif I[mejorGlobal].a()[1] > fiAve:
                    mejorGlobal= i
        generacion+= 1
    print('X*: ', I[mejorGlobal].p())
    print('f(x*): ',I[mejorGlobal].a()[0])
    print('Penalizacion total: ', fi(I[mejorGlobal].p(), True))
    fs.append(I[mejorGlobal].a()[0])
print('Media: %.3f'%(mean(fs)))
print('Desviación Estandar: %.3f'%(pstdev(fs)))
print('Mejor: %.3f \t Peor: %.3f'%(np.min(fs),np.max(fs)))
```

    Función a optimizar:  g13
    semilla:  0
    X*:  [-1.62881182  1.54090847 -1.61726013 -0.38987029  1.55585979]
    f(x*):  0.019099561690441948
    h1:  0.21565574405502375
    h2:  0.5408676970047654
    h3:  0.3374481502394593
    Penalizacion total:  1.0939715912992485
    semilla:  1
    X*:  [-0.72126247 -0.94886463 -2.69053015  0.45652575  1.07686739]
    f(x*):  0.37956423631552766
    h1:  0.027575271397395795
    h2:  0.09486044660794635
    h3:  0.22951949367491897
    Penalizacion total:  0.7424853650757082
    semilla:  2
    X*:  [-1.77904866  1.66391382  1.70725921 -0.81850667 -0.70029807]
    f(x*):  0.05519782404676464
    h1:  0.008727915274318931
    h2:  0.025260992471468402
    h3:  0.023987103693055012
    Penalizacion total:  0.057976011438842345
    semilla:  3
    X*:  [-1.66706579  1.53688884 -1.91333639 -0.69348224  0.85256515]
    f(x*):  0.05511459299582984
    h1:  0.0097767233370476
    h2:  0.015608585639842243
    h3:  0.0027830225335989667
    Penalizacion total:  0.02816833151048881
    semilla:  4
    X*:  [-1.72615668  1.60936607 -1.83094781  0.67989719 -0.85339143]
    f(x*):  0.04395151295388568
    h1:  0.11258303279366544
    h2:  0.04557309295386425
    h3:  0.02506777302461405
    Penalizacion total:  0.18322389877214373
    semilla:  5
    X*:  [-1.75708524  1.66621762 -1.63349532 -1.09048335  0.47744374]
    f(x*):  0.08291740748210571
    h1:  0.050956864789794665
    h2:  0.11853642701265965
    h3:  0.20115403680087152
    Penalizacion total:  0.37064732860332583
    semilla:  6
    X*:  [-0.81970052 -0.90977194  2.58671132 -1.28764337  0.39655262]
    f(x*):  0.2936022658770396
    h1:  0.005948785555418112
    h2:  0.19977438595735153
    h3:  0.30376867136331454
    Penalizacion total:  0.7962031584207514
    semilla:  7
    X*:  [-1.49217106  1.34244112  1.96898211 -0.39794423 -1.90520153]
    f(x*):  0.05027096808716506
    h1:  1.6937656728097341
    h2:  1.1475772177430805
    h3:  0.09684783534324248
    Penalizacion total:  2.938190725896057
    semilla:  8
    X*:  [-1.73526244  1.59874731 -1.56271423 -1.43855251  0.4006868 ]
    f(x*):  0.08217229887403019
    h1:  0.23918768749275365
    h2:  0.38365983163699324
    h3:  0.13872385159906653
    Penalizacion total:  0.7615713707288134
    semilla:  9
    X*:  [-1.61160521  1.47299928  1.99962     0.76151204  1.02843741]
    f(x*):  0.02429206473047325
    h1:  0.4030624584828697
    h2:  0.9703985057952211
    h3:  0.010230105590064653
    Penalizacion total:  1.3836910698681555
    semilla:  10
    X*:  [-0.72798918 -0.88898299 -2.23281105 -0.21626053 -1.91538066]
    f(x*):  0.5496062650977815
    h1:  0.021155885148644415
    h2:  0.08617514554108685
    h3:  0.08836619646035704
    Penalizacion total:  0.1956972271500883
    semilla:  11
    X*:  [-1.68740151  1.58694431 -1.85748899  0.6500327  -0.91825175]
    f(x*):  0.05135704716015013
    h1:  0.08171025153204603
    h2:  0.036736733803417465
    h3:  0.1919696962851889
    Penalizacion total:  0.3104166816206524
    semilla:  12
    X*:  [-1.10587325  0.75286183 -2.60032986 -1.34575743  0.34740688]
    f(x*):  0.40958170191216087
    h1:  0.4832265713431898
    h2:  0.3799378178658712
    h3:  0.07428884071489761
    Penalizacion total:  1.23778309116636
    semilla:  13
    X*:  [-1.62740291  1.49629655  1.8892176   1.13165702  0.52396729]
    f(x*):  0.06848739106635772
    h1:  0.011676067380923527
    h2:  0.13792653965133939
    h3:  0.03998408753711136
    Penalizacion total:  0.18958669456937427
    semilla:  14
    X*:  [-1.20025757  1.22069145  2.40434384  0.91253328  0.65660711]
    f(x*):  0.09289775396131926
    h1:  0.02457497621754179
    h2:  0.060917224111528334
    h3:  1.0898242996104774
    Penalizacion total:  1.2796603381410643
    semilla:  15
    X*:  [-1.87272358  1.7572268  -1.52718608  0.68537666 -0.78762453]
    f(x*):  0.06634072526518459
    h1:  0.01733052211818098
    h2:  0.015485059794270839
    h3:  0.1417711775579873
    Penalizacion total:  0.1745867594704391
    semilla:  16
    X*:  [-1.46573877  1.2514973   1.88624539  0.38858217  1.35034086]
    f(x*):  0.16274650369438953
    h1:  0.7530261432039431
    h2:  0.2629608687555969
    h3:  0.18882673591670307
    Penalizacion total:  1.204813747876243
    semilla:  17
    X*:  [-2.16591474  2.16890723  0.70995781 -0.42705783 -0.83231469]
    f(x*):  0.30560372081006393
    h1:  0.7745114545287972
    h2:  0.23739990102402486
    h3:  1.042173148857282
    Penalizacion total:  2.0540845044101044
    semilla:  18
    X*:  [-1.44127331  1.16759568  2.27014166  0.82018698  0.83169134]
    f(x*):  0.07383262985695109
    h1:  0.041491275343537026
    h2:  0.7601044528610554
    h3:  0.4021525921379985
    Penalizacion total:  1.203748320342591
    semilla:  19
    X*:  [-1.32090405  1.19584518 -2.11647753 -0.34538547  1.48883956]
    f(x*):  0.13298214900880656
    h1:  0.009755312902118618
    h2:  0.04013835764557161
    h3:  0.4054163267500994
    Penalizacion total:  0.45530999729778965
    semilla:  20
    X*:  [-1.14994058  0.73166772 -2.611996    0.52447287 -0.97748272]
    f(x*):  0.09702245146179202
    h1:  0.08923167984414349
    h2:  0.6522026847081952
    h3:  0.12894997280866694
    Penalizacion total:  1.1823803354923486
    semilla:  21
    X*:  [-1.84230433  1.70843985 -1.60618687  0.59860989 -0.91294923]
    f(x*):  0.05217339945513248
    h1:  0.0844982995137773
    h2:  0.011571465203596976
    h3:  0.26640053970417377
    Penalizacion total:  0.36247030442154804
    semilla:  22
    X*:  [-1.46922128  1.15355394 -2.24269025  0.97776228 -0.56179149]
    f(x*):  0.12395122983660173
    h1:  0.209413810056164
    h2:  0.15942848632476414
    h3:  0.6364586057863066
    Penalizacion total:  1.0053009021672348
    semilla:  23
    X*:  [-0.71978693 -0.83306753  2.74925171 -1.0023356   0.44554188]
    f(x*):  0.4789267070772922
    h1:  0.026336090606772444
    h2:  0.05739991390703025
    h3:  0.0489331287507887
    Penalizacion total:  0.5819208399529728
    semilla:  24
    X*:  [-1.64811027  1.46878947 -1.22875462  1.2980877  -0.37496525]
    f(x*):  2.288257946935232e-05
    h1:  1.790921483074225
    h2:  0.6289070524779194
    h3:  0.30802636533481387
    Penalizacion total:  2.7278549008869586
    semilla:  25
    X*:  [-0.9302266  -0.88487237 -2.5454616  -1.03537448 -0.6941869 ]
    f(x*):  0.2218070503575623
    h1:  0.3184088575101178
    h2:  1.3413083822535667
    h3:  0.49779937256648155
    Penalizacion total:  2.402978208267789
    semilla:  26
    X*:  [-1.90564792  1.82030315  1.28867078  0.48166811  0.9594012 ]
    f(x*):  0.04224763884539949
    h1:  0.24187526459705744
    h2:  0.03520665313989424
    h3:  0.1112319384654219
    Penalizacion total:  0.3883138562023736
    semilla:  27
    X*:  [-1.68882161  1.57765751  1.86748826 -0.69955835 -0.8589129 ]
    f(x*):  0.04853900820826936
    h1:  0.05574729286690783
    h2:  0.058041567542041594
    h3:  0.11007533150542947
    Penalizacion total:  0.2238641919143789
    semilla:  28
    X*:  [-0.80965491 -1.00809997  2.87113815 -1.13446908  0.78920945]
    f(x*):  0.12267959435200984
    h1:  1.8251125616559882
    h2:  1.5822743382870885
    h3:  0.555259307303638
    Penalizacion total:  4.533784360256801
    semilla:  29
    X*:  [-1.74028665  1.61739999  1.83921785  0.7183378   0.82570856]
    f(x*):  0.04639211084257788
    h1:  0.22510647311765553
    h2:  0.009062604491027137
    h3:  0.03953755744797682
    Penalizacion total:  0.2737066350566595
    semilla:  30
    X*:  [-1.62693668  1.57554286  1.82818495  1.35419199  0.43338785]
    f(x*):  0.06182176455247866
    h1:  0.49317945249819317
    h2:  0.054068005540112374
    h3:  0.6046495530054701
    Penalizacion total:  1.1518970110437756
    Media: 0.139
    Desviación Estandar: 0.142
    Mejor: 0.000 	 Peor: 0.550

