# Introdução à Computação Grafica - Tarefa 1


Este repositório se destina a apresentar os resultados da implementação de algoritmos de rasterização de pontos e linhas. Sendo motivado pela "Tarefa 1" da disciplina de Introdução à Computação Gráfica na UFPB.Para tal, foi disponibilizado pelo prof. Christian Azambuja Pagot, um Framework destinado à simular o acesso direto à memória de video, visto que os sistemas operacionais bloqueiam tal operação. Portanto, a rasterização aqui apresentada será, de maneira simulada, uma escrita direta na memória de video.




# O Pixel

Em computação , um pixel se define como o menor elemento de uma imagem.Sendo um agrupamento deles, a própria imagem. A ele, é possível definir uma cor, através de uma combinação de três elementos (red, green e blue) que recebem, cada um, valores de 0 a 255 e formam o Padrão RGB. Por exemplo, sabemos que a cor Amarelo é o resultado da sobreposição das cores Vermelho e Verde. Desta forma, Amarelo pode ser representada no padrão RGB como (255, 255, 0). Ou seja, a união dos níveis máximos de vermelho e verde, e o azul com intensidade 0. Além dos três elementos básicos, um quarto elemento representativo da transparencia, pode ser adicionado. Assim, formamos o RGBA.Visto que cada elemento pode assumir 256 níveis, é preciso de 8 bits para cada elemento, nos dando 4 bytes por pixel. 



# A exibição do Pixel (PutPixel)

Inicialmente, no arquivo mygl.h foram definidos dois structs para definir uma cor em RGBA e a posicão do pixel em coordenadas x e y. Desta forma, podemos definir um método (PutPixel) que exibe um pixel na tela, recebendo como parametro a posição do pixel e sua respectiva cor. 

Nele, através do ponteiro FBptr, obtemos acesso aos pixels no framebuffer. Tal ponteiro aponta inicialmente para o canto superior esquerdo da tela, a posição (0,0).

Como cada pixel da tela ocupa quatro bytes, devido as quatro componentes de cores (RGBA), devemos utilizar a posição horizontal do pixel, multiplicada por 4 para posicionar horizontalmente. Para se posicionar verticalmente, devemos multiplicar nossa coordenada por quatro e pela largura de pixels da tela. Por fim, devemos somar ambos os valores, pois o framebuffer é uma matriz unidimensional, fazendo o posicionamento dos pixels horizontalmente. Devemos somar a esse valor, respectivamente, 0, 1, 2 e 3 para valorar os componentes RGBA do pixel.

Como resultado da implementação, temos:

![PutPixel](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/putPixels.png)




# Desenhando retas - DrawLine

Para exemplificarmos, podemos tomar uma reta R, sendo definida do ponto (120, 120) para o ponto (250, 225). 

Quando se deseja desenhar uma linha na tela, pensamos inicialmente em escrever um pixel na tela, lado a lado, até chegar no ponto final na reta. No entanto, em retas cujo angulo não é um angulo reto, a reta iria passar por dois pixels em uma mesma coluna e não saberiamos qual seria o pixel correto.

Por exemplo, a implementação da reta R, nos mostra

![ErroDrawLine](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/erro_drawline.png)

Quando deveria mostrar:

![CorretoDrawLine](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/drawline_correto.png)

A correção do problema é feita utilizando-se do algoritmo de Bresenham, pois tal solução apresentada ser a forma mais eficiente de se resolver a questão, dispensando o uso de operações custosas que poderiam solucionar o problema do modo anterior.

Para entender, podemos considerar uma reta que interceptada duas colunas de pixel, para cada coluna, existe um pixel acima ou abaixo. A decisão sobre qual pintar, se tomada com base no cálculo da distância entre ambos, acarretaria em uma alta carga computacional. Portanto, o algoritmo toma como base o critério do ponto médio e, dessa forma, observando a localização do ponto médio entre o par de pixels em relação a reta. Caso o ponto médio esteja abaixo, pinta-se o pixel superior. Caso contrário, escolhe-se o pixel inferior. 
O algoritmo, porém, apenas funciona para retas de 0 a 45 graus. Ou seja, retas do primeiro octante. É preciso então generalizar o algoritmo.

Portanto, o método DrawLine recebe dois pontos de coordenadas x e y e duas cores para interpolação.



# Generalização do Algoritmo de Bresenham

Dividindo o plano carteziano em 8 octantes, temos:

![Octantes](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/octantes.png)


Para o octante 2, basta inverter os valores de x e y, assim, a reta continua tendo menos que 45°, mas reprensentando 90°. Os Octantes acima do eixo x e a direita do eixo y (7 e 8), podem ser representados com o algoritmo os octantes 1 e 2, porém, com a reta crescendo negativamente (pois o y cresce para baixo). Assim, basta decrementar a posição y do pixel, quando este era incrementado.É possível notar que as retas a esquerda do eixo Y (Octantes 3 a 6) são as mesmas retas a direita do eixo x, apenas invertidas. Bastando então, fazer com que o deltaX seja invertido, para resolver o problema.

O resultado é o seguinte:

![DrawLineCompleto](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/drawline_completo.png)




# Desenhando Triangulos - DrawTriangle

Com o algoritmo funcionando, é bastante simples desenhar triangulos na tela. Bastando gerar um método que recebe como parametro 3 pontos de coordenadas x e y e 3 cores para interpolação. O metodo, então traça linhas entres os vértices, para formar um triangulo.

Tomemos como exemplos os seguintes pontos:
Ponto A com valores (150,150) e a cor red
Ponto B com valores (450, 240) e a cor blue
Ponto C com valores (250, 300) e a cor green

As retas são traçadas através do método que implementamos para rastelização de retas

DrawLine(pontoA, ponto B, corA, corB);
DrawLine(pontoA, ponto C, corA, corC);
DrawLine(pontoB, ponto C, corB, corC);

Como resultado, temos:

![DrawTriangle](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/drawtriangle1.png)
![DrawTriangle](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/drawtriangle2.png)




# Outras Aplicações  

Podemos também desenvolver outras formas de utilizar os algoritmos desenvolvidos aqui. Um exemplo é a utilização para desenhar arcos e circulos preenchidos ou não.Para isso, foi criado o método DrawCircle que recebe como parametros um ponto central, duas cores para interpolação, o raio  e o angulo que o arco deve traçar - No caso de uma circunferencia, 360.

A implementação é simples! Partindo do ponto central C, basta calcular o ponto P de coordenadas X e Y que serão, respectivamente, o raio*cos(angulo*PI/180) e o raio*sen(angulo*PI/180).A partir daí, basta um laço que traça retas do ponto central C, até o ponto P, incrementando o angulo de 0 até o angulo escolhido como criterio de parada. 

![Circle](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/circle_green_black.png)
![Circle](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/circle_magenta_blue.png)

Uma outra implementação é o desenho de circunferencias não preenchidas, bastando trocar o DrawLine() por DrawPixel no algoritmo da circunferencia.

![CircleVazio](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/circulo_vazio.png)




# Considerações Finais

A implementação do putPixel é demasiadamente simples, no entanto o algoritmo de bresenham trás dificuldades no que diz respeito à generalização do mesmo. A partir dele, as implementações ficam mais interessantes, tanto é que cheguei ao método dos circulos com certa facilidade, a dificulde foi lembrar de coodenadas polares, mas nada que um Youtube não ajude. Houve, também, uma certa dificuldade em começar a usar o framework, mas os erros foram solucionados com a ajuda de colegas. 

Eu busquei desenhar um triangulo preenchido, como seria proposto como um extra. Obtive sucesso para algumas situações e de duas maneiras:

- Quando buscava entender o algoritmo de bresenham, fiz alguns testes e percebi que, desenhando linhas partindo de um ponto fixo e imcrementando x ou y, tinha um triangulo retangulo. Consegui generalizar para alguns triangulos, mas não chegava a todos os casos;
- A outra forma foi a partir do algoritmo do circulo, sendo alterado para a reta ir até mais que um ponto fixo e não mais o raio. O algoritmo funcionava, mas o triangulo era feito ainda com base nos angulos e não em pontos fixos.Talvez seja possível generalizar o código fazendo o processo inverso (coordenadas cartesianas para polares).Seguirei tentato e espero um dia conseguir, pois fiquei curioso. O resultado parcial foi o seguinte:

![TentativaTriangulo](https://github.com/yrflx/CG-Tarefa-1/raw/master/Printscreens/triangulopreenchido_tentativa.png)

Por: Yuri Félix Ramalho de Oliveira, aluno de Ciência da Computação, na UFPB.
_____________________________________________________________________________________________________________________________
# REFERÊNCIAS 

- Slides do Prof. Christian;
- https://en.wikipedia.org/wiki/Bresenham%27s_line_algorithm;
- Buscas aleatórias sobre coordenadas polares ;
- Blogs de ex-alunos da disciplina;


