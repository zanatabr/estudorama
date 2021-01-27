# Welcome to The Django - Henrique Bastos
## Turma Steve Wozniak - 2019

- Antigo: https://welcometothedjango.com.br/hackerspace
- Atual: http://henriquebastos.nutror.com/
- [Small Acts Manifesto ver](http://smallactsmanifesto.org/)


[Instalação do PyCharm](https://www.jetbrains.com/help/pycharm/installation-guide.htm)

```
$ sudo snap install pycharm-community --classic
```


# Módulo 1 - Apaixone-se pelo Python

## 1.1. Conheça a sua jornada

## 1.2. Por que Python

"Python fits your mind" - Bruce Eckel

"Parece que os programadores que adotam linguagens dinâmicas são mais felizes." - Bruce Eckel

A linguagem foi criada por Guido van Rossun (Holandes), enquanto trabalhava em um projeto chamado "Amoeba" (Sistema Operacional Distribuído) do Instituto de Computação da Holanda. A sua responsabilidade era desenvolver aplicativos para administrar o sistema, e a camada que os programadores usariam para desenvolver as aplicações. Isso tudo estava sendo desenvolvido em C, mas isso estava se tornando burocrático e complicado, principalmente no que diz respeito ao tratamento de erros. 

No mesmo laboratório haviam desenvolvido o "ABC", uma linguagem  de altíssimo nível, que tinha a proposta de isolar a complexidade da máquina do programador, mas havia uma dificuldade em extender a linguagem.

Durante um feriado entre Natal e Ano Novo o Guido tentou implementar uma linguagem nova, utilizando o que aprendeu e achou interessante do "ABC", e trazendo elementos de outras linguagens também, com o objetivo de criar uma linguagem fácil, que possibilitasse a prototipação rápida de programas, e que fosse facilmente extendida. 

Marco principal da linguagem: Mensagem de e-mail enviada para a comunidade em 1991 (https://groups.google.com/forum/#!msg/alt.sources/Dl7XnUUbLik/2tWvvDwnOyEJ). Nasceu como código livre.


-- Animação: 
-- History of Python - Gource - development visualization (august 1990 - june 2012)
https://www.youtube.com/watch?v=cNBtDstOTmA

-- The Zen of Python --
https://www.python.org/dev/peps/pep-0020/


-- Easter Egg
>>> import this


-- PyPI - Python Package Index
Repositório público em que são publicados os módulos
https://pypi.org/


-- The Python Standard Library
https://docs.python.org/3/library/



== 1.3. Na dúvida, veja como se encontrar ==


-- Python documentation (bom baixar)
https://docs.python.org

-- Dash (DocSet para Mac)
Ferramenta que centraliza documentações

-- Zeal (DocSet Livre - inspirado no Dash, que usa os mesmos pacotes)
https://zealdocs.org/
$ sudo apt-get install zeal



== 1.4. Como instalar o Python no Windows ==
== 1.5. Como instalar o Python no Mac ==
== 1.6. Como instalar o Python no Linux ==
== 1.7. O Interpretador Python ==



== 1.8. Python em uma página ==

-- arquivo: peup.py  (Python Em Uma Página)
import os

def main():
    print('Hello world!')
    print("This is Alice's greeting.")
    print('This is Bob\'s greeting.')


    foo(5, 10)

    print('=' * 10)
    text = 'The current working directory is '
    print(text + os.getcwd())

    foods = ['apples', 'oranges', 'cats']

    for food in foods:
        print('I like to eat', food)

    print('Count to ten:')
    for i in range(1, 11):
        print(i)

def foo(a, b):
    value = a + b

    print('%s plus %s is equal to %s' % (
        a, b, value))

    if value < 50:
        print('foo')
    elif (value >= 50) and \
         ((a == 42) or (b == 42)):
        print('bar')
    else:
        print('moo')


    '''A multi-
    line string, but can also be a
    multi-line comment.'''

    return value # This is a one line comment

if __name__ == '__main__':
    main()



Análise:

1 - Qual é a primeira coisa a ser executada?
No Python, o entry-point é o módulo, não o método main (como em C ou Java).

Exemplo: Um módulo "meuprograma.py" com uma única linha (print). Ao ser chamada pelo interpretador Python:

$ python meuprograma.py

O entry point do interpretador é exatamente o "meuprograma.py"

2 - Mas o que acontece ao chamar o interpretador sem nada (modo interativo)?

$ python

O interpretador cria um "módulo vazio", e qualquer variável definida, será definida no namespace do módulo. O namespace é sempre do módulo. Isso muda um pouco o conteito que se tem sobre variáveis globais, porque as variáveis globais do Python, são globais para o módulo, e não de todo o processo.


>>> globals
<built-in function globals>

-- Ao executar a função, ela lista o que existe no namespace atual
>>> globals()
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>}


-- Após criar uma variável, perceber a modificação no namespace
>>> mensagem = 'Python r0x'
>>> globals()
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, 'mensagem': 'Python r0x'}




== 1.9. A grande sacada dos Módulos ==

No exemplo é feita a importação do módulo "os".

No modo interativo, se simplesmente digitarmos "os", o interpretador vai nos dizer que não existe nenhuma variável "os" no namespace atual.

>>> os
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'os' is not defined


Pra resolver isso é necessário fazer um "import"

>>> import os

>>> os
<module 'os' from '/home/zanata/.pyenv/versions/3.6.10/lib/python3.6/os.py'>

Quando perguntado novamente sobre o nome "os", o interpretador indica que "os" referencia o objeto módulo no path especificado (o módulo é associado a um arquivo .py).

No Python, um módulo é também um objeto. Então é possível usar o ".", que é o acessor, para referenciar algum recurso interno do módulo.

>>> os.getcwd()
'/home/zanata'


Para verificar que um módulo é um oobjeto como outro qualquer, podemos usar o comando "type" para dizer qual é o tipo de objeto que a variável "os" referencia.

>>> type(os)
<class 'module'>


Atributos especiais do módulo/objeto

-- Nome do módulo
>>> os.__name__
'os'

-- Caminho completo que originou o módulo
>>> os.__file__
'/home/zanata/.pyenv/versions/3.6.10/lib/python3.6/os.py'



-- O que acontece quando digito "import os"?

O Python procura no Python Path se existe um módulo com esse nome. Inicialmente a busca é feita no diretório atual, e depois procura em lugares específicos da biblioteca padrão. No caso do "os", foi encontrada na "biblioteca padrão" do Python.

Depois de encontrado o arquivo, faz o processamento/parser de todo o texto do arquivo, gera os bytecodes, e instancia um "objeto módulo" que faz referência aos bytecodes gerados.

Para que isso não seja feito a todo o momento, porque é algo custoso, o Python cria um cache de todos os módulos que já foram importados.

-- Mais opções do comando "import"

a)  >>> import os

Busca um módulo com o nome "os", e depois de instanciar esse objeto na memória, cria uma variável local chamada "os", que faz referência a esse módulo.

b)  >>> import os as meuos

Possibilita fazer a importação do módulo "os", mas usando outro nome

    >>> meuos
    <module 'os' from '/home/zanata/.pyenv/versions/3.6.10/lib/python3.6/os.py'>

Agora temos uma variável "meuos" que referencia o objeto módulo "os" no runtime.

Como no Python tudo é referência, é possível também fazer algo do tipo:

    >>> xpto = meuos

    >>> xpto
    <module 'os' from '/home/zanata/.pyenv/versions/3.6.10/lib/python3.6/os.py'>

Agora temos a variável "xpto" também referenciando o módulo "os".

    >>> xpto.getcwd()
    '/home/zanata'

Quando fazemos:

    >>> import os

O que ocorre nos bastidores é:

    >>> import os as os

A forma inicial seria apenas uma forma reduzida, mais simples.

É importante ter em mente que o mecanismo de "import" cria um objeto no runtime do Python. É o interpretador que cuida desse objeto.

No nosso namespace local, teremos apenas uma variável que referencia esse objeto.

>>> os, meuos, xpto
(<module 'os' from '/home/zanata/.pyenv/versions/3.6.10/lib/python3.6/os.py'>, 
<module 'os' from '/home/zanata/.pyenv/versions/3.6.10/lib/python3.6/os.py'>, 
<module 'os' from '/home/zanata/.pyenv/versions/3.6.10/lib/python3.6/os.py'>)

-- referenciam o mesmo objeto
>>> id(os), id(meuos), id(xpto)
(140697143652088, 140697143652088, 140697143652088)



c) >>> from os import getcwd

É possível importar apenas um recurso específico do módulo, em vez de importar o módulo inteiro.

Agora temos uma variável "getcwd" no namespace que referencia o objeto que está no módulo "os".

    >>> getcwd
    <built-in function getcwd>

    >>> getcwd()
    '/home/zanata'

Seria o mesmo que:

    >>> from os import getcwd as getcwd

Então, também épossível definir um outro nome:

    >>> from os import getcwd as meudir

    >>> meudir()
    '/home/zanata'

    >>> id(getcwd), id(meudir), id(os.getcwd)
    (140697143851984, 140697143851984, 140697143851984)


d) Mas qual é a diferença entre importar o módulo todo ou componentes do módulo?

Em termos de consumo de processamento, não muda nada, pois o Python terá que fazer todo o processo descrito anteriormente para importar um módulo. 

O que diferencia é a quantidade que teremos no "namespace".

Se usarmos muitas coisas de um módulo, então é interessante importar o módulo inteiro. Agora, se usarmos uma ou duas coisas do módulo, pode ser mais prático pegar simplesmente os recursos que nos interessa, e evitar o uso de acessores (".") no código.


== 1.10. Por dentro do import ==

Entendendo o fluxo do "import"

-- arquivo: proga.py
print ('Begin', __name__)

print('Define fA')
def fA():
    print('Dentro de fA')

print('Chama fA')
fA()

print ('End', __name__)



Se executarmos o proga.py

$ python proga.py 
Begin __main__
Define fA
Chama fA
Dentro de fA
End __main__


Análise:

Foi impresso "__main__" e não "proga". O nome do módulo é "__main__" e não "proga". Isso acontece porque como "proga" é o "entry point" do programa, o Python sempre sobrescreve o nome do módulo para de entry point para "__main__".


-- arquivo: progb.py
print ('Begin', __name__)

print('Define fB')
def fB():
    print('Dentro de fB')

print('Chama fB')
fB()

print ('End', __name__)


Execução de progb.py

$ python progb.py 
Begin __main__
Define fB
Chama fB
Dentro de fB
End __main__




-- arquivo: progb.py (modificado)
print ('Begin', __name__)
import proga

print('Define fB')
def fB():
    print('Dentro de fB')
    proga.fA()

print('Chama fB')
fB()

print ('End', __name__)


Execução de progb.py

$ python progb.py 
Begin __main__
Begin proga
Define fA
Chama fA
Dentro de fA
End proga
Define fB
Chama fB
Dentro de fB
Dentro de fA
End __main__



Análise: Perceber que o "import" de "proga", não apenas "processa/analisa" o módulo, mas "executa" todo o módulo durante a sua importação. Somente no final da execução de "proga" é que o controle é devolvido para o "progb" e a sua execução continua.


Como fazer para impedir essa situação?


-- arquivo: proga.py (modificado)
print ('Begin', __name__)

print('Define fA')
def fA():
    print('Dentro de fA')

if __name__ == '__main__':
    print('Chama fA')
    fA()

print ('End', __name__)


Execução de "progb"

$ python progb.py 
Begin __main__
Begin proga
Define fA
End proga
Define fB
Chama fB
Dentro de fB
Dentro de fA
End __main__


Funcionou da forma adequada.



Execução de "proga" como entry point:

$ python proga.py 
Begin __main__
Define fA
Chama fA
Dentro de fA
End __main__


Funcionou da forma adequada. O teste "if __name__ == '__main__':" verifica se o módulo atual é um entry point ou biblioteca.



== 1.11. Mais do que Strings ==

- Declaração de Strings: Entre 'Aspas Simples' ou "Aspas Duplas"
- "Escape" em situações específicas  \'  \"
- Em Python, todas as strings são Unicode
- As strings em Python são imutáveis. Qualquer operação aplicada em uma string, não a modifica, mas gera uma nova string.


>>> nome = 'zanata'
>>> type(nome)
<class 'str'>

-- criando a instância de uma string a partir de um inteiro
>>> str(1)
'1'

-- criando a instância de uma string a partir de um ponto flutuante
>>> str(1.5)
'1.5'

>>> 'zanata'.encode()
b'zanata'

-- Toda string é Unicode
>>> 'aviação'.encode()
b'avia\xc3\xa7\xc3\xa3o'


>>> nome
'zanata'

-- métodos disponíveis para string
>>> nome.
nome.capitalize(    nome.isalnum(       nome.join(          nome.rsplit(
nome.casefold(      nome.isalpha(       nome.ljust(         nome.rstrip(
nome.center(        nome.isdecimal(     nome.lower(         nome.split(
nome.count(         nome.isdigit(       nome.lstrip(        nome.splitlines(
nome.encode(        nome.isidentifier(  nome.maketrans(     nome.startswith(
nome.endswith(      nome.islower(       nome.partition(     nome.strip(
nome.expandtabs(    nome.isnumeric(     nome.replace(       nome.swapcase(
nome.find(          nome.isprintable(   nome.rfind(         nome.title(
nome.format(        nome.isspace(       nome.rindex(        nome.translate(
nome.format_map(    nome.istitle(       nome.rjust(         nome.upper(
nome.index(         nome.isupper(       nome.rpartition(    nome.zfill(

>>> nome.upper()
'ZANATA'

>>> nome
'zanata'

>>> nome.capitalize()
'Zanata'

>>> 'adalberto zanata'.title()
'Adalberto Zanata'

>>> nome = 'adalberto zanata'

>>> nome.replace('t','TT')
'adalberTTo zanaTTa'

>>> nome.split()
['adalberto', 'zanata']

>>> nome.split('a')
['', 'd', 'lberto z', 'n', 't', '']

-- Concatenação (menos eficiente)
>>> 'adalberto' + ' ' + 'zanata'
'adalberto zanata'


-- Concatenação (mais eficiente)
>>> ' '.join(['adalberto', 'zanata'])
'adalberto zanata'

>>> '\n'.join(['adalberto', 'zanata'])
'adalberto\nzanata'

>>> print( '\n'.join(['adalberto', 'zanata']) )
adalberto
zanata

>>> nome
'adalberto zanata'

>>> len(nome)
16




== 1.12. Simplifique tudo com Sequências ==

- Strings são objetos, mas também são sequências

>>> nome = 'giulia'

>>> len(nome)
6

-- retorna o primeiro elemento da sequência "nome"
>>> nome[0]
'g'

>>> nome[1]
'i'

-- Tenta acessar um índice além do limite
>>> nome[6]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range


>>> nome[len(nome)-1]
'a'

-- é o mesmo que:
>>> nome[-1]
'a'

-------------------------
 0  1  2  3  4  5  
 g  i  u  l  i  a
-6 -5 -4 -3 -2 -1
-------------------------

Usando o "slice" (fatia da sequência):

-- pegar uma fatia do 1o elemento "até"" o 5o elemento (índice 4)

>>> nome[0:4]
'giul'

A descrição do intervalo é: Fechado no início (inclui o primeiro elemento), mas é aberto no final (não inclui o índice 4).

O mesmo ocorre com índices negativos:

>>> nome[1:-1]
'iuli'

>>> nome[1:len(nome)]
'iulia'

>>> len(nome)
6

>>> nome[1:]
'iulia'

>>> nome[0:4]
'giul'

>>> nome[:4]
'giul'

>>> nome[:]
'giulia'

>>> nome[1:5:2]
'il'

>>> nome[1:6:2]
'ila'

>>> nome[1:-1:2]
'il'

>>> nome[::2]
'gui'

>>> nome[::-1]
'ailuig'



-- Por baixo dos panos: métodos especiais

>>> len
<built-in function len>

>>> len(nome)
6

>>> nome.__len__()
6

Nunca se deve acessar os métodos com "__".


Vide mensagens de erro para perceber como fica mais clara a mensagem ao usar corretamente a função:

>>> pi = 3.14

>>> pi.__len__()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'float' object has no attribute '__len__'

>>> len(pi)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: object of type 'float' has no len()



Outros exemplos:

>>> nome[0]
'g'

-- é um açúcar sintático para:
>>> nome.__getitem__(0)
'g'


>>> nome[1:-1:2]
'il'

-- idem
>>> idx = slice(1, -1, 2)

>>> type(idx)
<class 'slice'>

>>> nome[idx]
'il'

>>> idx
slice(1, -1, 2)

>>> idx.
idx.indices(  idx.start     idx.step      idx.stop   


In [3]: idx.indices?                        
Docstring:
S.indices(len) -> (start, stop, stride)

Assuming a sequence of length len, calculate the start and stop
indices, and the stride length of the extended slice described by
S. Out of bounds indices are clipped in a manner consistent with the
handling of normal slices.
Type:      builtin_function_or_method


idx.indices é a função que consegue "normalizar" o slice:

>>> idx
slice(1, -1, 2)

São índices relativos: 1, -1 , 2

Quando usamos a notação

>>> nome[1:-1:2]
'il'

Internamente o Python faz:

>>> idx.indices(len(nome))
(1, 5, 2)

>>> nome.__getitem__(slice(1, -1, 2))
'il'






== 1.13. Como ler código indentado ==

No Python, a legibilidade conta, e isso tem a ver com a sua origem (a linguagem ABC). O ABC era parte de um projeto de pesquisa que investigava quais eram as pegadinhas das linguagens que mais atrapalhavam os programadores, e falta de clareza no código estava no topo da lista. O BEGIN..END e as "chaves" são indicadores que delimitam o bloco de código, explicitam a relação de hierarquia entre partes do código. 

O Python não precisa de um símbolo para isso. Usa um conceito de design muito importante que diz que "espaço em branco" também tem informação, e o ser humano detecta isso logo de cara. Fica claro que há uma relação hierárquica entre uma linha e as demais que foram indentadas logo abaixo dela.

Como o Python controla isso?

Todo comando composto, termina com ":", e este símbolo é o delimitador de bloco. A existência de ":" indica para o Python que será iniciado um novo bloco de código na linha seguinte, usando 4 espaços para indentação (vide PEP 0008).


Dá pra criar um bloco vazio?
Sim. Com o uso do "pass"

     for i in range(1,11):
         pass

Evitar o uso do TAB. Configure o TAB do editor para ser substituir o caractere de TAB para 4 caracteres de espaços.


-- Linhas Físicas vs. Linhas Lógicas

Linhas Físicas: Qualquer linha que termine com "\n"

Linhas Lógicas: Terminadas com "\" ou que deixaram um parênteses aberto para ser fechado na próxima linha (conforme recomendação da PEP 0008)

Exemplos:

    if value < 50:
        print('foo')
    elif (value >= 50) and \
         ((a == 42) or (b == 42)):
        print('bar')
    else:
        print('moo')

ou:

    if value < 50:
        print('foo')
    elif ((value >= 50) and 
         ((a == 42) or (b == 42))):
        print('bar')
    else:
        print('moo')        




== 1.14. Listas ==

- Lista são sequências mutáveis.
- Múito úteis e oferecem um excelente desempenho.
- No caso de objetos mutáveis, é comum que os métodos alterarem o estado interno do objeto e retornar 'none'. O "sort" e o "append" fazem isso.


-- Declaração de uma Lista
>>> ['A', 'B', 'C']
['A', 'B', 'C']

>>> lst = ['A', 'B', 'C']

>>> type(lst)
<class 'list'>

>>> lst.append('D')

>>> lst
['A', 'B', 'C', 'D']

>>> lst.sort(reverse=True)

>>> lst
['D', 'C', 'B', 'A']

>>> lst.sort()

>>> lst
['A', 'B', 'C', 'D']

>>> print(lst.append('E'))
None

>>> lst
['A', 'B', 'C', 'D', 'E']

A lista armazena referências para os objetos:

>>> ['A', 1, 3.14, 10j, len, [1, 2, 3]]
['A', 1, 3.14, 10j, <built-in function len>, [1, 2, 3]]


A Lista é uma sequência, então aceita as mesmas operações

>>> lst[0]
'A'

>>> lst[-1]
'E'

>>> lst[1:]
['B', 'C', 'D', 'E']

-- Retorna uma cópia da Lista, com referências para o mesmos objetos
>>> lst[:]
['A', 'B', 'C', 'D', 'E']



-- Objetos mutáveis nos ajudam a entender um pouco o comportamento do Python em relação a referências.

>>> m = lst

>>> lst, m
(['A', 'B', 'C', 'D', 'E'], ['A', 'B', 'C', 'D', 'E'])

>>> id(lst), id(m)
(140619739869896, 140619739869896)

>>> m.append('F')

>>> lst, m
(['A', 'B', 'C', 'D', 'E', 'F'], ['A', 'B', 'C', 'D', 'E', 'F'])


>>> def f(x):
...     x.append(42)
...     return x
... 


>>> f(lst), lst
(['A', 'B', 'C', 'D', 'E', 'F', 42], ['A', 'B', 'C', 'D', 'E', 'F', 42])


-- Para evitar esse comportamento

>>> def g(x):
...     x = x[:]
...     x.append(51)
...     return x
... 


>>> g(lst), lst
(['A', 'B', 'C', 'D', 'E', 'F', 42, 51], ['A', 'B', 'C', 'D', 'E', 'F', 42])



-- Tipos mutáveis dentro da lista

>>> l = [1, 2, [4, 5, 6]]

>>> l
[1, 2, [4, 5, 6]]

>>> m = l[-1]

>>> m
[4, 5, 6]

>>> m.append(42)

>>> m
[4, 5, 6, 42]

>>> l
[1, 2, [4, 5, 6, 42]]




== 1.15. Tuplas ==

- São sequências imutáveis


-- Declaração de uma Tupla
>>> ('A', 'B', 'C')
('A', 'B', 'C')


-- Tuplas são sequências imutáveis

>>> t = ('A', 'B', 'C')

>>> t + ('D', 'E')
('A', 'B', 'C', 'D', 'E')


-- Métodos da tupla (basicamente): count e index
>>> t.
t.count(  t.index(  


>>> type(t)
<class 'tuple'>


In [1]: tuple?                                                                                  
Init signature: tuple(self, /, *args, **kwargs)
Docstring:     
tuple() -> empty tuple
tuple(iterable) -> tuple initialized from iterable's items

If the argument is a tuple, the return value is the same object.
Type:           type
Subclasses:     int_info, float_info, hash_info, version_info, flags, thread_info, asyncgen_hooks, waitid_result, stat_result, statvfs_result, ...


>>> tuple('lucca')
('l', 'u', 'c', 'c', 'a')

>>> tuple([1, 2, 3])
(1, 2, 3)

-- Tuplas armazenam qualquer tipo de objeto
>>> ('A', 1, 3.14, (2j, len), [1, 2, 3])
('A', 1, 3.14, (2j, <built-in function len>), [1, 2, 3])


-- Índices na tupla e slice
>>> print(t[0], t[1], t[2])
A B C

>>> t[1:]
('B', 'C')

>>> u = t[:]

>>> id(t), id(u)
(139656192461920, 139656192461920)


-- Quem constrói a tupla é a vírgula e não o parênteses. 
>>> 'C', 'D', 'E'
('C', 'D', 'E')

>>> t= 'C', 'D', 'E'

>>> t
('C', 'D', 'E')


-- Os parênteses são usados por conta da precedência dos operadores
>>> 'A', 'B' + t
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: must be str, not tuple

>>> ('A', 'B') + t
('A', 'B', 'C', 'D', 'E')


-- E se eu quiser criar uma tuplia de um único elemento? 
>>> 'A',
('A',)

-- mas não pode esquecer a vírgula no final
>>> ('A',)
('A',)

-- , caso contrário, é só uma string (neste caso)
>>> ('A')
'A'

-- Criar uma tupla vazia
>>> tuple()
()

>>> ()
()


>>> type(())
<class 'tuple'>





== 1.16. Dicionários ==

-- Dicionários são hash maps; armazenam chave e valor (pares)
>>> {}
{}


-- Dicionário não tem ordem
>>> d = {'nome': 'Giulia', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6}

>>> d
{'nome': 'Giulia', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6}


-- Acessar uma chave do dicionário
>>> d['nome']
'Giulia'


-- Atualizar o valor de uma chave
>>> d['nome'] = 'Giulia Zanata'

>>> d
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6}


-- Verificar se uma chave ou um valor está no dicionário
>>> d['xyz']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'xyz'

>>> 'xyz' in d
False

>>> 'nome' in d
True


-- Valor padrão do método get
>>> 'São Paulo' in d.values()
True

>>> d.get('xyz')

>>> print(d.get('xyz'))
None

>>> print(d.get('xyz', 'valor padrão'))
valor padrão

>>> len(d)
4

>>> d
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6}


-- Dictionary views (visão dinâmica): d.keys(), d.values() e d.items()

>>> d.keys()
dict_keys(['nome', 'cidade', 'lat', 'long'])

>>> d.values()
dict_values(['Giulia Zanata', 'São Paulo', 22.8, 43.6])

>>> d.items()
dict_items([('nome', 'Giulia Zanata'), ('cidade', 'São Paulo'), ('lat', 22.8), ('long', 43.6)])


>>> k = d.keys()
>>> v = d.values()
>>> i = d.items()

>>> k
dict_keys(['nome', 'cidade', 'lat', 'long'])

>>> v
dict_values(['Giulia Zanata', 'São Paulo', 22.8, 43.6])

>>> i
dict_items([('nome', 'Giulia Zanata'), ('cidade', 'São Paulo'), ('lat', 22.8), ('long', 43.6)])

>>> d['Olá'] = 'Mundo!'

>>> d
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6, 'Olá': 'Mundo!'}

>>> k
dict_keys(['nome', 'cidade', 'lat', 'long', 'Olá'])

>>> v
dict_values(['Giulia Zanata', 'São Paulo', 22.8, 43.6, 'Mundo!'])

>>> i
dict_items([('nome', 'Giulia Zanata'), ('cidade', 'São Paulo'), ('lat', 22.8), ('long', 43.6), ('Olá', 'Mundo!')])



-- Dictionary views não dinâmicos (transformados em tuplas) 
>>> k = tuple(d.keys())

>>> v = tuple(d.values())

>>> i = tuple(d.items())

>>> k
('nome', 'cidade', 'lat', 'long', 'Olá')

>>> v
('Giulia Zanata', 'São Paulo', 22.8, 43.6, 'Mundo!')

>>> i
(('nome', 'Giulia Zanata'), ('cidade', 'São Paulo'), ('lat', 22.8), ('long', 43.6), ('Olá', 'Mundo!'))



-- Apagar uma chave-valor de um dicionário: comando del 
>>> d
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6, 'Olá': 'Mundo!'}

>>> del d['Olá']

>>> d
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6}


-- Chaves precisam ser imutáveis
>>> l = [1, 2, 3]

>>> d[l] = 'Pode?'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'

>>> hash(l)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'

>>> hash((1,2,3))
2528502973977326415

>>> hash('Lucca')
-4809749176119168929


-- Já os valores podem ser mutáveis
>>> d.update(interesses=['autonomia','hack'])

>>> d
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6, 'interesses': ['autonomia', 'hack']}

>>> d['interesses']
['autonomia', 'hack']

>>> d['interesses'].append('cerveja')

>>> d
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6, 'interesses': ['autonomia', 'hack', 'cerveja']}

-- pop() : Retorna o valor e já o remove do dicionário
>>> d.pop('interesses')
['autonomia', 'hack', 'cerveja']

>>> d
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6}



-- Formas de construir um dicionário
>>> t = tuple(d.items())

>>> t
(('nome', 'Giulia Zanata'), ('cidade', 'São Paulo'), ('lat', 22.8), ('long', 43.6))

>>> dict(t)
{'nome': 'Giulia Zanata', 'cidade': 'São Paulo', 'lat': 22.8, 'long': 43.6}


>>> dict(nome='Lucca Zanata', cidade='São Paulo')
{'nome': 'Lucca Zanata', 'cidade': 'São Paulo'}




== 1.17. O Sistema de Tipos: Dinâmico e Forte ==

Sistema de Tipos é um conjunto de regras para definir como os elementos de um programa se relacionam. A ideia é reduzir os bugs, explicitando as interfaces que são verificadas para garantir que as coisas façam sentido.

-- No Python a tipagem é dinâmica
Em tempo de execução o Python verifica os tipos.


>>> letras = 'abc'
# se fosse em C, por exemplo, deveríamos especificar o tipo
# char letras = 'abc';

-- Mas como o Python consegue resolver isso?
Como ele sabe que "letras" é uma string? Como ele sabe que possui um método upper?

>>> letras.upper()
'ABC'

Isso é identificado dinamicamente. Ao se deparar com com o código  "letras.upper()", o Python identifica qual é o objeto associado a variável "letras", em seguida verifica que possui um acessor ".", e é o acessor que permite acessar algo de dentro do objeto. Com o acessor o Python verifica se há um método "upper" associado ao objeto referenciado por "letras". Sabendo que o método existe, e sabe quem é, pode aplicar o operador "parênteses" para executar o método no contexto de "letras" e devolver o resultado.

Essa é a "tipagem dinâmica" em ação.


-- No Python a tipagem é forte

O Python não fica convertendo os seus objetos de um tipo em outro implicitamente:

>>> 1 + '1'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'int' and 'str'


-- Quero somar dois números ou concatenar duas strings?
>>> 1 + int('1')
2
>>> str(1) + '1'
'11'


-- Sobrecarga de operadores

>>> '1' + str(1)
'11'

>>> int('1') + 1
2

-- Como o Python sabe qual operador realizar? 
Essa notação é um "açúcar sintático" que não revela o que é feito por baixo dos panos.

>>> '1'.__add__(str(1))
'11'

>>> int('1').__add__(1)
2

O Python suporta sobrecarga de operadores, ou seja, podemos implementar uma classe, e reimplementar todos os operadores que fazem sentido para essa classe, para que seja usada a sintaxe do Python e fazer um código mais expressivo. Isso evita a reinvenção de nomes de métodos, quando podemos simplesmente usar a sintaxe do Python.


>>> 3 * 42
126

>>> '#' * 42
'##########################################'

>>> int(3).__mul__(42)
126

>>> '#'.__mul__(42)
'##########################################'

>>> '1' + '1'
'11'

>>> 1 + 1
2

>>> '1' * 42
'111111111111111111111111111111111111111111'

>>> 3 * 42
126


Esses detalhes são implementados nas classes, e a sobrecarga de operadores podem ser implementados nas suas próprias classes para que o código fique mais expressivo.

É preciso tomar cuidado para não "ressignificar demais" os operadores, utilizando-os de forma pouco intuitiva.

>>> 3 % 2
1

>>> int(3).__mod__(2)
1

-- A código seguir já não é tão intuitivo
>>> 'Olá, %s!' % 'Lucca'
'Olá, Lucca!'

>>> 'Olá, %s! %s' % ('Lucca', 'Bom dia!')
'Olá, Lucca! Bom dia!'

>>> 'Olá, %s! %s'.__mod__(('Lucca', 'Bom dia!'))
'Olá, Lucca! Bom dia!'



-- Métodos mágicos

- The Python Language Reference
https://docs.python.org/3/reference/

- 3.3. Special method names
https://docs.python.org/3/reference/datamodel.html#special-method-names

Procurar por "__add__" e verificar demais métodos que podem ser implementados.


-- Tipagem estática vs Python

Muitos acreditam que a "Tipagem Estática" (onde se define os tipos na sintaxe da linguagem) é mais segura. De fato, o compilador tem mais informações pra "barrar" o "programador" e compilar o programa somente se as interfaces estiverem de acordo. O inconveniente é a burocracia no processo de escrita do código.

No Python a "Tipagem é Dinâmica" (verifica os tipos durante a execução). Entretanto, oPython tem "Tipagem Forte", o que garante a facilidade de não precisar definir os tipos na sintaxe, e a segurança de não ter os objetos convertidos implicitamente.

Para quem não está convencido disso, ver palestra "Wat" de @garybernhardt:

- Wat - A lightning talk by Gary Bernhardt from CodeMash 2012
https://www.destroyallsoftware.com/talks/wat?fbclid=IwAR23JipnrzZVS799jIYQzprKQXLBIdGM3YH8L3FLOINLbIxalPXjothY1Ss




== 1.18. Loops radicais ==

-- While

- arquivo: loop.py
nome = 'marianne'

print(nome, len(nome))

i = 0

while i < len(nome):
    print(nome[i])
    i += 1


Execução:
$ python loop.py 
marianne 8
m
a
r
i
a
n
n
e




-- For

- arquivo: loop.py (modificado)
nome = 'marianne'

print(nome, len(nome))

for i in range(len(nome)):
    print(nome[i])

Execução:
$ python loop.py 
marianne 8
m
a
r
i
a
n
n
e



-- Função range

Range é uma função especial built-in do Python que retorna um intervalo.

>>> range(8)
range(0, 8)

>>> r = range(8)

>>> r[0]
0

>>> r[-1]
7

>>> list(r)
[0, 1, 2, 3, 4, 5, 6, 7]


- Mas e se o range for muito grande? Não causará um estouro de memória?

O range produz os elementos programaticamente, ou seja, somente quando são acessados. Se tivésemos um sequência de 1 milhão de números, e uma lista de 1 milhão de números, teríamos uma ocupação exagerada de memória. Agora, com o range, podemos criá-lo para um intervalo de 1 milhão de números, e num dado momento poderíamos ter apenas um elemento na memória.

- Veja o help do range:

In [1]: range?                                                                                  
Init signature: range(self, /, *args, **kwargs)
Docstring:     
range(stop) -> range object
range(start, stop[, step]) -> range object

Return an object that produces a sequence of integers from start (inclusive)
to stop (exclusive) by step.  range(i, j) produces i, i+1, i+2, ..., j-1.
start defaults to 0, and stop is omitted!  range(4) produces 0, 1, 2, 3.
These are exactly the valid indices for a list of 4 elements.
When step is given, it specifies the increment (or decrement).
Type:           type
Subclasses:     


>>> range(1, 20, 3)
range(1, 20, 3)

>>> list(range(1, 20, 3))
[1, 4, 7, 10, 13, 16, 19]

-- vai de 1 (intervalo fechado) até 22 (intervalo aberto)
>>> list(range(1, 22, 3))
[1, 4, 7, 10, 13, 16, 19]


>>> for i in range(8):
...     print(i)



O "for" recebe na sua direita qualquer objeto "iterável". Por exemplo, as strings, porque elas também são sequências (de caracteres)

- arquivo: loop.py (modificado)
nome = 'marianne'

print(nome, len(nome))

for c in nome:
    print(c)




Execução
$ python loop.py 
marianne 8
m
a
r
i
a
n
n
e



No Python é muito raro precisar contralar índices, ou acessar as coisas por índices em um loop. Geralmente tem uma forma "mais alto nível" para isso.


-- Função enumerate: mostra o índice da iteração

E em uma situação que se precisa saber também o índice, além do próprio elemento?



- arquivo: loop.py (modificado)
nome = 'marianne'

print(nome, len(nome))

for c in enumerate(nome):
    print(c)



Execução:    
$ python loop.py 
marianne 8
(0, 'm')
(1, 'a')
(2, 'r')
(3, 'i')
(4, 'a')
(5, 'n')
(6, 'n')
(7, 'e')


-- Fazendo a expansão:

- arquivo: loop.py (modificado)
nome = 'marianne'

print(nome, len(nome))

for i, c in enumerate(nome):
    print(i, c)


Execução:
$ python loop.py 
marianne 8
0 m
1 a
2 r
3 i
4 a
5 n
6 n
7 e


- Como o "enumerate" funciona:

Todo iterator implementa o "__next__", um método especial do Python.


>>> enumerate('camila')
<enumerate object at 0x7f043ba778b8>

>>> e = enumerate('camila')

>>> e
<enumerate object at 0x7f043ba779d8>

>>> next(e)
(0, 'c')

>>> next(e)
(1, 'a')

>>> next(e)
(2, 'm')

>>> next(e)
(3, 'i')

>>> next(e)
(4, 'l')

>>> next(e)
(5, 'a')

>>> next(e)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration

Quando não tem mais nada a ser acessado, estoura uma exceção "StopIteration", que é tratada implicitamente pelo laço "for".


-- Break e continue

- arquivo: loop.py (modificado)
nome = 'marianne'

print(nome, len(nome))

for i, c in enumerate(nome):
    if c == 'i':
        continue
    print(i, c)



Execução:
$ python loop.py 
marianne 8
0 m
1 a
2 r
4 a
5 n
6 n
7 e




- arquivo: loop.py (modificado)
nome = 'marianne'

print(nome, len(nome))

for i, c in enumerate(nome):
    if c == 'i':
        break
    print(i, c)



Execução:
$ python loop.py 
marianne 8
0 m
1 a
2 r




== 1.19. Decisões e expressões lógicas ==

"=" :: atribuição
"==" :: equivalência

-- If e else

- arquivo: if.py
nome = 'lucca zanata'
for c in nome:
    if c == 'e' or c == 'l' or c == 'z':
        print(c.upper())
    else:
        print(c)

Execução:
$ python if.py
L
u
c
c
a
 
Z
a
n
a
t
a



- arquivo: if.py (modificado)
nome = 'lucca zanata'
for c in nome:
    if c in ('e', 'l', 'z'):
        print(c.upper())
    else:
        print(c)

Execução:
$ python if.py
L
u
c
c
a
 
Z
a
n
a
t
a



- arquivo: if.py (modificado) - Strings também são sequências
nome = 'lucca zanata'
for c in nome:
    if c in 'elz':
        print(c.upper())
    else:
        print(c)


Execução:
$ python if.py
L
u
c
c
a
 
Z
a
n
a
t
a



-- Elif


- arquivo: if.py (modificado)
nome = 'lucca zanata'
for c in nome:
    if c in 'aeiou':
        print(c.upper())
    elif c == 'z':
        print('@')
    else:
        print(c)


Execução:
$ python if.py
l
U
c
c
A
 
@
A
n
A
t
A



-- Expressões lógicas: and e or

>>> True and True
True

>>> True and False
False

>>> True or True
True

>>> True or False
True

>>> False or False
False


-- True e False

"True" e "False" são objetos singletons no Python. Há somente uma instância de cada um deles na execução.

Qualquer objeto em Python possui uma representação lógica. Qualquer coisa que seja vazia, zero ou nula é False.

>>> bool(None)
False

>>> bool(False)
False

>>> bool(0)
False

>>> bool('')
False

>>> bool(())
False

>>> bool([])
False

>>> bool({})
False

Qualquer outro valor é True.

>>> bool(1)
True

>>> bool(['abc'])
True


-- Curto circuito do "and" e do "or" no Python


>>> True and 'abc'
'abc'

Mas por que retornou 'abc'?
O operador lógico no Python sempre retorna o último elemento avaliado. No caso do "and", pra ser verdadeiro, é necessário que os dois elementos sejam verdadeiros. Neste caso "True" é verdadeiro e a string 'abc' tem a representação lógica de verdadeiro. Como avalia a string 'abc' por último, retorna a string 'abc'


>>> True and []
[]


E neste caso? A "lista vazia" tem a representação lógica de falso. Avaliou o "True" (OK), e quando avaliou o segundo elemento verificou que era falso, e retornou o segundo elemento.


>>> 'abc' and True
True


>>> [] and True
[]

Avalia o primeiro elemento (lista vazia) e já retorna como "falso". Então, faz um curto-circuito e nem mesmo avalia o segundo elemento, pois a expressão "and" requer que os dois elementos sejam verdadeiros.


>>> 1 or []
1

>>> 1 or indefinido
1

>>> indefinido
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'indefinido' is not defined

>>> 1 and indefinido
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'indefinido' is not defined

>>> indefinido = True

>>> 1 and indefinido
True



== 1.20. Faça mágica com atribuições inteligentes ==


-- arquivo: atribuicao.py
# cria uma tupla
row = 'Lucca', 'São Paulo', 30.1, 45.8
print(row)


Execução:
$ python atribuicao.py 
('Lucca', 'São Paulo', 30.1, 45.8)



-- arquivo: atribuicao.py (modificado)
# cria uma tupla
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    print(nome, cidade, lat, log)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Traceback (most recent call last):
  File "atribuicao.py", line 8, in <module>
    f(row)
  File "atribuicao.py", line 5, in f
    print(nome, cidade, lat, log)
NameError: name 'nome' is not defined



-- arquivo: atribuicao.py (modificado)
# cria uma tupla
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # Como passar da tupla para esses nomes?
    # A forma mais simples seria:
    nome = t[0]
    cidade = t[1]
    lat = t[2]
    long = t[3]
    print(nome, cidade, lat, long)

if __name__ == '__main__':
    f(row)



Execução:
$ python atribuicao.py 
Lucca São Paulo 30.1 45.8


-- Atribuições múltiplas

O Python suporta múltiplas atribuições emuma só linha:

-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    nome, cidade, lat, long = t[0], t[1], t[2], t[3]
    print(nome, cidade, lat, long)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Lucca São Paulo 30.1 45.8


Dá pra deixar ainda mais interessante


-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    nome, cidade, lat, long = t
    print(nome, cidade, lat, long)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Lucca São Paulo 30.1 45.8




-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # neste caso, perdemos a vantagem
    nome, cidade = t
    print(nome, cidade)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Traceback (most recent call last):
  File "atribuicao.py", line 9, in <module>
    f(row)
  File "atribuicao.py", line 5, in f
    nome, cidade = t
ValueError: too many values to unpack (expected 2)



-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # neste caso, perdemos a vantagem
    nome, cidade = t[0], t[1]
    print(nome, cidade)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Lucca São Paulo



-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # usando slice. apenas os 2 elementos iniciais
    nome, cidade = t[:2]
    print(nome, cidade)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Lucca São Paulo




-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # e se quisermos o primeiro e o último
    nome, long = t[0], t[3]
    print(nome, long)

if __name__ == '__main__':
    f(row)




Execução:
$ python atribuicao.py 
Lucca 45.8




-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # e se quisermos o primeiro e o último
    nome, long = t[0], t[-1]
    print(nome, long)

if __name__ == '__main__':
    f(row)



Execução:
$ python atribuicao.py 
Lucca 45.8


-- Underscore na atribuição (_ e *_)


-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # usando o "_" como variável para 
    # descartar os valores indesejados da tupla
    # O "_" é um nome de variável válido no Python
    nome, _, _, long = t
    print(nome, long)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Lucca 45.8



-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # usando o "_" como variável para 
    # descartar os valores indesejados da tupla
    # O "_" é um nome de variável válido no Python
    nome, _, _, long = t
    print(nome, long, _)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Lucca 45.8 30.1


E se tivermos "muitos itens" para serem descartados?

-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # usando o "*" para descartar
    # os valores indesejados da tupla
    nome, *meio, long = t
    print(nome, long, meio)

if __name__ == '__main__':
    f(row)

Execução:
python atribuicao.py 
Lucca 45.8 ['São Paulo', 30.1]



-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # usando o "*" para descartar
    # os valores indesejados da tupla
    *nome, _, long = t
    print(nome, long, _)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
['Lucca', 'São Paulo'] 45.8 30.1




-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # só para verificar o comportamento
    # não é o que se deseja
    *nome, _, long = t
    print(nome, long, _)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
['Lucca', 'São Paulo'] 45.8 30.1



-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    # agora sim
    *_, lat, long = t
    print(lat, long, _)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
30.1 45.8 ['Lucca', 'São Paulo']



-- arquivo: atribuicao.py (modificado)
row = 'Lucca', 'São Paulo', 30.1, 45.8

def f(t):
    nome, *_ = t
    print(nome, _)

if __name__ == '__main__':
    f(row)


Execução:
$ python atribuicao.py 
Lucca ['São Paulo', 30.1, 45.8]




-- arquivo: atribuicao.py (modificado)
table = (('Lucca', 'Atibaia', 30.1, 45.8),
         ('Giulia', 'Jundiaí', 2.4, 54.7))

for row in table:
    nome = row[0]
    cidade = row[1]
    lat = row[2]
    long = row[3]
    print(nome, cidade, lat,long)
    
Execução:
$ python atribuicao.py 
Lucca Atibaia 30.1 45.8
Giulia Jundiaí 2.4 54.7




-- arquivo: atribuicao.py (modificado)
table = (('Lucca', 'Atibaia', 30.1, 45.8),
         ('Giulia', 'Jundiaí', 2.4, 54.7))

for row in table:
    nome, cidade, lat,long = row
    print(nome, cidade, lat,long)


Execução:
$ python atribuicao.py 
Lucca Atibaia 30.1 45.8
Giulia Jundiaí 2.4 54.7




-- Unpacking no for


-- arquivo: atribuicao.py (modificado)
table = (('Lucca', 'Atibaia', 30.1, 45.8),
         ('Giulia', 'Jundiaí', 2.4, 54.7))

for nome, cidade, lat,long in table:
    print(nome, cidade, lat,long)


Execução:
$ python atribuicao.py 
Lucca Atibaia 30.1 45.8
Giulia Jundiaí 2.4 54.7




-- arquivo: atribuicao.py (modificado)
table = (('Lucca', 'Atibaia', 30.1, 45.8),
         ('Giulia', 'Jundiaí', 2.4, 54.7))

for nome, _, lat,long in table:
    print(nome, lat,long, _)



Execução:
$ python atribuicao.py 
Lucca 30.1 45.8 Atibaia
Giulia 2.4 54.7 Jundiaí






-- arquivo: atribuicao.py (modificado)
table = (('Lucca', 'Atibaia', 30.1, 45.8),
         ('Giulia', 'Jundiaí', 2.4, 54.7))

for nome, *_ in table:
    print(nome, _)



Execução:
$ python atribuicao.py 
Lucca ['Atibaia', 30.1, 45.8]
Giulia ['Jundiaí', 2.4, 54.7]




== 1.21. Não subestime as funções ==

-- Funções



- arquivo: funcoes.py
def f():
    return 42

print( f() )


Execução:
$ python funcoes.py 
42



-- Toda função retorna alguma coisa

Se o retorno não for explícito, a função retornará None.

É possível definir uma função de corpo vazio (pass)

- arquivo: funcoes.py (modificado)
def f():
    pass

print( f() )

Execução:
$ python funcoes.py 
None



-- Definição de função com argumentos posicionais e argumentos nomeados


:: Parâmetros posicionais: A ordem dos parâmetros combina com a ordem dos argumentos

- arquivo: funcoes.py (modificado)
def f(a, b, c):
    print(a, b, c)

f('A', 'B', 'C')


Execução:
$ python funcoes.py 
A B C


:: Parâmetros nomeados: Associa explicitamente o argumento com o valor que está sendo passado. Vantagem: a ordem não importa.

- arquivo: funcoes.py (modificado)
def f(a, b, c):
    print(a, b, c)

f(b='B', c='C', a='A')

Execução:
$ python funcoes.py 
A B C


-- Valores default na função


- arquivo: funcoes.py (modificado)
def f(a, b, c='dC'):
    print(a, b, c)

f('A', 'B')

f(b='B', a='A')


Execução:
$ python funcoes.py 
A B dC
A B dC


-- Passar quantidade indefinida de parâmetros posicionais: *args

O nome "args" não é obrigatório, é uma convenção da comunidade para parâmetros posicionais indefinidos.

- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', *args):
    print(a, b, c, args)

f('A', 'B', 'C', 'D', 'E', 'D')


Execução:
$ python funcoes.py 
A B C ('D', 'E', 'D')



-- Passar quantidade indefinida de parâmetros nomeados: **kwargs

O nome "kwargs" não é obrigatório, é uma convenção da comunidade.

- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', **kwargs):
    print(a, b, c, kwargs)

f(z='Z', c='C', b='B', a='A', d='D', e='E', m='M')


Execução:
$ python funcoes.py 
A B C {'z': 'Z', 'd': 'D', 'e': 'E', 'm': 'M'}




- deixando de passar valor para um parâmetro requerido
- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', **kwargs):
    print(a, b, c, kwargs)

f(z='Z', c='C', b='B', d='D', e='E', m='M')


Execução:
$ python funcoes.py 
Traceback (most recent call last):
  File "funcoes.py", line 4, in <module>
    f(z='Z', c='C', b='B', d='D', e='E', m='M')
TypeError: f() missing 1 required positional argument: 'a'




- misturando o posicional com o nomeado
- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', **kwargs):
    print(a, b, c, kwargs)

f('A', z='Z', c='C', b='B', d='D', e='E', m='M')

Execução:
$ python funcoes.py 
A B C {'z': 'Z', 'd': 'D', 'e': 'E', 'm': 'M'}



- misturando o posicional com o nomeado, mas duplicando a passagem
- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', **kwargs):
    print(a, b, c, kwargs)

f('A', 'B', z='Z', c='C', b='B', d='D', e='E', m='M')

Execução:
$ python funcoes.py 
Traceback (most recent call last):
  File "funcoes.py", line 4, in <module>
    f('A', 'B', z='Z', c='C', b='B', d='D', e='E', m='M')
TypeError: f() got multiple values for argument 'b'



-- Misturando *args e **kwargs


- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', *args, **kwargs):
    print(a, b, c, args, kwargs)

f('A', 'B', 'C', 'D', 'E', z='Z', m='M')


Execução:
$ python funcoes.py 
A B C ('D', 'E') {'z': 'Z', 'm': 'M'}



-- Keyword only argument
Surgiu no Python 3. Indica um argumento que obrigatoriamente deve ser passado de forma nomeada.

- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', *args, x, y, **kwargs):
    print(a, b, c, x, y, args, kwargs)

f('A', 'B', 'C', 'D', 'E', z='Z', m='M')


Execução:
$ python funcoes.py 
Traceback (most recent call last):
  File "funcoes.py", line 4, in <module>
    f('A', 'B', 'C', 'D', 'E', z='Z', m='M')
TypeError: f() missing 2 required keyword-only arguments: 'x' and 'y'



- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', *args, x, y, **kwargs):
    print(a, b, c, x, y, args, kwargs)

f('A', 'B', 'C', 'D', 'E', x=1, y=2, z='Z', m='M')


Execução:
$ python funcoes.py 
A B C 1 2 ('D', 'E') {'z': 'Z', 'm': 'M'}



- arquivo: funcoes.py (modificado)
def f(a, b, c='dC', *args, x=42, y=51, **kwargs):
    print(a, b, c, x, y, args, kwargs)

f('A', 'B', 'C', 'D', 'E', x=1, y=2, z='Z', m='M')

Execução:
$ python funcoes.py 
A B C 1 2 ('D', 'E') {'z': 'Z', 'm': 'M'}




O Django usa o "kwargs", por exemplo, na função "filter".

Simulação:

- arquivo: funcoes.py (modificado)
def filter(**lookups):
    for k, v in lookups.items():
        print(k.split('__'), v)


filter(name__startswith='Hen', age__lt=30,
        city__endswith='rói')


Execução:
$ python funcoes.py 
['name', 'startswith'] Hen
['age', 'lt'] 30
['city', 'endswith'] rói


-- Passando dados para a função

- arquivo: funcoes.py (modificado)
def f(*args, **kwargs):
    print(args, kwargs)

t = 'A', 'B', 'C'
d = dict(z='Z', w='W')

f(t, d)


Execução:
$ python funcoes.py 
(('A', 'B', 'C'), {'z': 'Z', 'w': 'W'}) {}

Não é bem o que queríamos, pois os dois argumentos foram passados como "args", e o "kwargs" ficou vazio.


-- Função genérica def(*args, **kwargs)


- arquivo: funcoes.py (modificado)
def f(*args, **kwargs):
    print(args, kwargs)

t = 'A', 'B', 'C'
d = dict(z='Z', w='W')

f(t[0], t[1], t[2], z=d['z'], w=d['w'])


Execução:
$ python funcoes.py 
('A', 'B', 'C') {'z': 'Z', 'w': 'W'}

Este último era o esperado, mas não está nada Pythônico.



-- Fazer o unpacking dos dados ao passar para a função

- arquivo: funcoes.py (modificado)
def f(*args, **kwargs):
    print(args, kwargs)

t = 'A', 'B', 'C'
d = dict(z='Z', w='W')

f(*t, **d)

Execução:
$ python funcoes.py 
('A', 'B', 'C') {'z': 'Z', 'w': 'W'}



-- Exemplo no terminal, mostrando atributos do objeto função


>>> def add(a, b):
...     return a + b
... 

>>> add
<function add at 0x7f043dbcda60>

>>> type(add)
<class 'function'>

>>> add.__code__
<code object add at 0x7f043dbe4ed0, file "<stdin>", line 1>

>>> add.__code__.co_argcount
2

>>> add.__code__.co_code
b'|\x00|\x01\x17\x00S\x00'

>>> add.__code__.co_name
'add'

>>> add.__code__.co_varnames
('a', 'b')


- Módulo "dis" (disassemble)

>>> import dis
>>> dis.dis(add)
  2           0 LOAD_FAST                0 (a)
              2 LOAD_FAST                1 (b)
              4 BINARY_ADD
              6 RETURN_VALUE




-- Doc string: documentação da função

Sempre que a primeira linha de uma função for uma string, o texto dessa string será usado como documentação da função (Doc String).

>>> def add(a, b):
...     'Soma a com b'
...     return a + b


>>> add
<function add at 0x7f043dbcdae8>


>>> add.__doc__
'Soma a com b'

>>> help(add)
Soma a com b


-- A importância da função como objeto

>>> def calc(op, a, b):
...     return op(a, b)

>>> calc(add, 2, 3)
5

>>> def mul(a, b):
...     return a * b
 
>>> calc(mul, 2, 3)
6



== 1.22. Um telefonema inesperado ==

-- Temos um projeto
-- O problema da Morena, a nossa cliente

A Morena está organizando um evento gigantesco para mais de 2000 pessoas chamado EVENTEX, e contratou uma equipe de "freelas" para fazer o sistema: desde o website até a gestão de inscritos, notificação por e-mail, etc. Essa equipe gastou 8 meses e agora, faltando 1 semana para entregar, não tem nada pronto.

Qual é o problema? A Morena já tem uma agenda de divulgação em revista, TV, etc. Os anúncios já foram contratados, não tem como mudar isso. 

Ela nos questionou se daria tempo, e o projeto foi vendido.

A entrega será feita sem negociar qualidade, e sem negociar prazo. Será negociado apenas o escopo. 

-- O primeiro passo: a landing page

E na primeira release, pra funcionar, pra preparar o terreno, pra estar tudo como a gente quer, para que possamos entrar no ciclo do desenvolvimento com entregas constantes, desenvolveremos uma "landing page".

E por que uma "landing page"?

A cliente precisa entregar um site para que as pessoas possam enxergar o que é o evento. Porque tudo será divulgado em revista, na TV, rádio, etc. A divulgação já está toda agendada pra acontecer. E hoje ela não tem nada.

Quando não se tem nada, um passo pra frente é MUITA EVOLUÇÃO!

Então, entregaremos uma "landing page", que é uma página com as informações do evento. E isso é muito importante.

Com essa primeira página, já conseguimos fazer a primeira rodada em todo o processo de iteração, conseguimos organizar o nosso projeto, fazer a entrega, e a partir daí é feature por feture, fazendo e implantando.


-- Ver o mundo com os olhos do cliente

Para que possamos ficar conectados com o valor que geramos para o nosso cliente, precisamos sair do nosso universo de tecnologia, e tentar olhar o mundo com os olhos do cliente.

Os olhos da Morena estão enxergando apenas as "datas", o pavor dela é o cronograma do evento que já está todo organizado, e não tem sistema para atender isso.

Precisamos criar formas de encaixar as nossas features alinhadas com cada necessidade, com cada etapa do cronograma. E tudo o que estiver definido no cronograma dela que levar muito tempo pra fazer, aproveitaremos para ganhar tempoe implementar as novas funcionalidades. 

-- O planejamento é de trás pra frente

Nos basearemos nas datas fornecidas por ela, e de trás para a frente verificamos o que "cabe" naquele tempo. 

Não dá pra chegar dizendo o que vai ter no sistema. Temos que ver o que o cliente precisa para o negócio ir pra frente, e a partir daí focamos apenas nisso, e exclusivamente nessa capacidade para entregar.




== 1.23. Conheça o Django ==

-- Django é um framework web de alto nível, escrito em Python

Django é um framework web de alto nível escrito em Python que possibilita rápido desenvolvimento e design pragmático.

"Framework de alto nível", faz com que o programador não se preocupe com muitos detalhes de como as coisas funciona por baixo dos panos na web, permitindo que seja dado mais foco no valor a ser gerado para o cliente, em vez de ficar cuidando dos detalhes de baixo nível.

Por ser escrito em Python, não devemos pensar no Django como um framework separado, como se fosse algo principal e considerando o Python como satélite. Na verdade é o contrario disso.

O Django dá continuidade à cultura do Python, continua o processo do ecossistema. Podemos pensar no framework como uma extensão natural, com o propósito web, no Python.

"... que possibilita rápido desenvolvimento..."
Não confundir "rápido desenvolvimento" com "desenvolvimento rápido".  Não se trata de fazer as coisas rápidas, mas sim do ciclo de desenvolvimento ter muito feedback, ser muito curto, o que nos possibilita perceber, a todo o momento, progresso no desenvolvimento. É através desse processo de ciclos curtos de feedback que conseguimos nos manter amarrados/próximos do valor que vamos gerar, tendo a certeza de que estamos sempre fazendo a coisa certa.

"... e design pragmático."
O Django não se preocupa em resolver sempre 100% das coisas, mas sim em resolver 80% das coisas que sempre acontecem em sistemas web, deixando espaço para que o programador resolva os 20% que somente ele precisa.

Assim o Django se organiza em várias camadas, permitindo que o programador interfira em cada uma delas, pra fazer exatamente o que for necessário.



-- A origem do Django

ver: https://www2.ljworld.com/news/2015/jul/09/happy-birthday-django/


Foi criado em 2005 por Adrian Holovaty (@adrianholovaty) e Simon Willison (@simonw). O Adrian trabalhava no Lawrence Journal-World (Kansas - https://www2.ljworld.com/), enquanto o Simon era estagiário do Jornal. 

O jornal possuia vários sistemas, desenvolvidos em diversas linguagens, principalmente PHP, e a manutenção foi ficando cada vez mais difícil devido a forma como as coisas foram acontecendo ao longo dos anos.

Devido a uma demanda urgente para a entrega de um HotSite, o Adrian resolveu fazer essa entrega usando Python. E não foi surpresa quando conseguiram fazer esse processo muito rápido e entregar o website em um tempo recorde.

Foram amadurecendo a ideia em novas demandas. A partir de necessidades reais, foram desenvolvendo cada vez mais componentes, extraindo os componentes repetitivos, aquilo que acontecia em todos os sites que desenvolviam, para gerar o framework, que no início era apenas um conjunto de classes.


-- A documentação do Django

O Simon saiu do projeto depois de algum tempo, e entrou o Jacob Kaplan-Moss (@jacobian :: http://jacobian.org), que trouxe vida nova ao Django. A sua formação é em linguística, e é um dos grandes responsáveis pela altíssima qualidade da documentação do Django. A documentação do Django é tão famosa, que praticamente se tornou um padrão do ecossistema Python, que os outros almejam ter algo parecido.

Toda a documentação do Django é gerada dinamicamente a partir do código fonte, e é levada tão a sério que nenhuma modificação/patch é aceita no sistema sem que seja feita a atualização da documentação. Então nunca teremos uma funcionalidade nova no Django sem uma documentação adequada pra ela.


-- Arquitetura MTV: model template view

Vide: diagrama01-DjangoArchitecture-JeffCroft.png
wttd--turma-steve-wozniak--henrique-bastos--2019-diagrama01-DjangoArchitecture-JeffCroft.png
imagens/wttd-sw-diagr010.png

Template :: camada de apresentação
View :: faz a orquestração do que vai ser exibido e como será exibido
Models :: Organizam como os dados estarão representados


-- Filosofias

ver: https://docs.djangoproject.com/pt-br/3.0/misc/design-philosophies/

1. Desenvolvimento rápido : Em que os ciclos de feedback seja, bem curtos e que a todo momento se consiga ver o que já aconteceu/como está o progresso.

2. Don't repeat yourself (DRY) : Evitar repetição, evitar código duplicado. É possível refatorar o Django a todo o momento para que não ocorram repetições. Nada de copy and paste.

3. Menos código : Dá para orquestrar qualquer uma das camadas do Django para criar maiores abstrações e ter mais expressividade no código.

4. Acoplamento fraco : Das diversas partes que compõem o framework, é possível trocar todas elas, pode influenciar todas elas e colocar coisas entre elas.

5. Consistência : Não importa qual parte do Django esteja sendo examinado, porque o código terá a "mesma cara". Dá para entender como está organizado e navegar através do código sem nenhum problema.

Sempre examinar o código fonte do framework para se aprofundar ou tirar dúvidas.


-- As versões do Django

Primeiras versões surgiram em 2003, mas só foi publicado em 2005 na PyCon em Chicago.

De 2005 a 2007 ficou na versão pré 1.0

Em 2008 foi lançada a release 1.0.

Daí em diante, quase anualmente, é lançada uma nova release do Django.

A manutenção da versão 1.x é importante para a comunidade, pois ela preza por uma evolução suave. Não tem muita prática de quebrar as coisas que ficaram pra trás. 

É bem prático pegar uma versão antiga do Django, um projeto feito em uma versão de 2 ou 3 anos atrás, e trazer para a versão atual. Se forem aplicadas as técnicas e usados os recursos que o Django "prega", é muito tranquilo fazer essa evolução/migração. Não tem motivo para o código ficar desatualizado.


O Django resolve a "parte chata" da web. E isso permite ao programador ficar focado em ser mais produtivo, e entregar mais valor aos clientes.




== 1.24. Supere o medo da Web ==


Entenda realmente como funciona os bastidores da navegação na internet e adapte o seu modelo mental para trabalhar com o protocolo HTTP.

-- Entendendo a web
-- Domain Name Service: DNS
-- Internet Protocol: IP
-- Transmission Control Protocol: TCP
-- HTTP Request: tudo é transmitido em texto
-- HTTP Response
-- Árvore DOM (Document Object Model) e árvore de renderização
-- Stateless vs Stateful



== 1.25. Show me the code ==

Hora de começar o projeto da nossa cliente organizando tudo como um Chef de Cozinha.

1. Criação do diretório de trabalho

$ mkdir ~/wttd
$ cd ~/wttd/



2. Criar um virtualenv para isolar todas as dependências do projeto

$ python -m venv .wttd


O VirtualEnv já vem na bibloteca padrão do Python 3. O nome do módulo é "venv".

Ao usar o nome do módulo "venv" com o parâmetro "-m", o módulo "venv" é indicado como entry point.

O parâmetro final (.wttd) é o nome do VirtualEnv, Foi usado o mesmo nome do diretório de trabalho, com um ponto na frente, para indicar que é um diretório oculto.

Isso é feito para separar todos os arquivos do VirtualEnv (que são várias) em um diretório isolado, para não misturar com o código do projeto.




3. Por dentro do .wttd

zanata@zanata-summa:~/wttd $ ls -la
total 12
drwxr-xr-x  3 zanata zanata 4096 mai 14 00:12 .
drwxr-xr-x 64 zanata zanata 4096 mai 14 00:10 ..
drwxr-xr-x  5 zanata zanata 4096 mai 14 00:12 .wttd


zanata@zanata-summa:~/wttd $ ls -la .wttd
total 24
drwxr-xr-x 5 zanata zanata 4096 mai 14 00:12 .
drwxr-xr-x 3 zanata zanata 4096 mai 14 00:12 ..
drwxr-xr-x 2 zanata zanata 4096 mai 14 00:12 bin
drwxr-xr-x 2 zanata zanata 4096 mai 14 00:12 include
drwxr-xr-x 3 zanata zanata 4096 mai 14 00:12 lib
lrwxrwxrwx 1 zanata zanata    3 mai 14 00:12 lib64 -> lib
-rw-r--r-- 1 zanata zanata  101 mai 14 00:12 pyvenv.cfg


zanata@zanata-summa:~/wttd $ ls -la .wttd/bin
total 40
drwxr-xr-x 2 zanata zanata 4096 mai 14 00:12 .
drwxr-xr-x 5 zanata zanata 4096 mai 14 00:12 ..
-rw-r--r-- 1 zanata zanata 2201 mai 14 00:12 activate
-rw-r--r-- 1 zanata zanata 1257 mai 14 00:12 activate.csh
-rw-r--r-- 1 zanata zanata 2421 mai 14 00:12 activate.fish
-rwxr-xr-x 1 zanata zanata  252 mai 14 00:12 easy_install
-rwxr-xr-x 1 zanata zanata  252 mai 14 00:12 easy_install-3.6
-rwxr-xr-x 1 zanata zanata  234 mai 14 00:12 pip
-rwxr-xr-x 1 zanata zanata  234 mai 14 00:12 pip3
-rwxr-xr-x 1 zanata zanata  234 mai 14 00:12 pip3.6
lrwxrwxrwx 1 zanata zanata   46 mai 14 00:12 python -> /home/zanata/.pyenv/versions/3.6.10/bin/python
lrwxrwxrwx 1 zanata zanata    6 mai 14 00:12 python3 -> python

No diretório "bin" ficam todos os executáveis: python, pip, easy_install (não será usado porque usaremos o pip) e o activate (que ativa o VirtualEnv)





4. Ativar o virtualenv

$ source .wttd/bin/activate

Perceber a modificação do prompt, que agora indica que o VirtualEnv está ativo e qual é o nome do VirtualEnv:

    (.wttd) zanata@zanata-summa:~/wttd $ 

$ which python
/home/zanata/wttd/.wttd/bin/python

Agora o Python usado é o Python do VirtualEnv, e não o Python do sistema. O mesmo se aplica ao "pip".


5. Instalar o Django

$ pip install django

O Django será instalado no virtualenv.




6. Criar o projeto Django (eventex)

=> django-admin startproject [nome projeto] [diretório trabalho]

$ django-admin startproject eventex .

Se não for indicado o "." como workdir, será criado um diretório de trabalho chamado "eventex" e nele será criado o projeto Django.

(.wttd) zanata@zanata-summa:~/wttd $ ls
eventex  manage.py

(.wttd) zanata@zanata-summa:~/wttd $ tree
.
├── eventex
│   ├── asgi.py
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py



Obs.1: Um diretório com __init__.py é reconhecido como um pacote Python
Obs.2: wsgi ==> lê-se "uisgui"

O manage.py é o entry point do DJango

$ python manage.py 

Type 'manage.py help <subcommand>' for help on a specific subcommand.

Available subcommands:

[auth]
    changepassword
    createsuperuser

[contenttypes]
    remove_stale_contenttypes

[django]
    check
    compilemessages
    createcachetable
    dbshell
    diffsettings
    dumpdata
    flush
    inspectdb
    loaddata
    makemessages
    makemigrations
    migrate
    sendtestemail
    shell
    showmigrations
    sqlflush
    sqlmigrate
    sqlsequencereset
    squashmigrations
    startapp
    startproject
    test
    testserver

[sessions]
    clearsessions

[staticfiles]
    collectstatic
    findstatic
    runserver



7. Acessando o manage.py de qualquer diretório

A ideia é a de facilitar o acesso ao "manage.py" a partir de qualquer diretório do projeto.

Criar um atalho para acessar o manage.py


$ echo $VIRTUAL_ENV
/home/zanata/wttd/.wttd

$ alias manage='python $VIRTUAL_ENV/../manage.py'

$ manage
Type 'manage.py help <subcommand>' for help on a specific subcommand.

Available subcommands:

[auth]
    changepassword
    createsuperuser

[contenttypes]
    remove_stale_contenttypes

[django]
    check
    compilemessa
...



8. Pra quem tem Windows ou Unix

Para Windows:
# Criar o arquivo wttd\.wttd\Scripts\manage.bat
@python "%VIRTUAL_ENV%..\manage.py" %*

Para UNIX
# Criar um alias em ~/.bashrc ou ~/.profile
alias manage='python $VIRTUAL_ENV/../manage.py'



9. Rodando o servidor de desenvolvimento do Django

$ manage runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

May 14, 2020 - 03:44:31
Django version 3.0.6, using settings 'eventex.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.



Enquanto isso, no navegador: 
==> http://127.0.0.1:8000/
(OK)

==> CTRL + C no terminal para encerrar o server




10. Criar uma app (dentro do diretório eventex)

$ cd ~/wttd/eventex/

Criar a primeira Django App.
Uma App no Django é uma biblioteca Python que segue algumas convenções do Django. Para criar uma App usamos:

==> manage startapp [nomeApp]

$ manage startapp core




11. A estrutura do diretório de trabalho

$ cd ~/wttd

$ tree
.
├── db.sqlite3
├── eventex
│   ├── asgi.py
│   ├── core
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── __init__.py
│   │   ├── migrations
│   │   │   └── __init__.py
│   │   ├── models.py
│   │   ├── tests.py
│   │   └── views.py
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-36.pyc
│   │   ├── settings.cpython-36.pyc
│   │   ├── urls.cpython-36.pyc
│   │   └── wsgi.cpython-36.pyc
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py

Perceber que o diretório "core" é um pacote Python (ver __init__.py)


wttd :: diretório de trabalho
eventex :: diretório do projeto (que tb é um pacote)
manage.py :: "irmão" do diretório eventex
core :: App,que é um subdiretório do projeto

Perceber a existência do arquivo "db.sqlite3". Quando foi executado o "runserver", o Python iniciou o projeto DJango, este por sua vez percebeu a configuração do BD e criou um BD SQLite de desenvolvimento.



12. Conferir se o PyCharm está usando o Python do virtualenv

Como o "wttd" já possui um VirtualEnv, o PyCharm consegue detectá-lo.

Abrir o projeto no PyCharm.

Para conferir isso: (Menu) File, Settings, Project: wttd, Project Interpreter.

Verificar se está apontando para o VirtualEnv que construímos (no meu caso ~/wttd/.wttd/bin/python)




13. Instalar/Configurar a app core

Criamos a App, agora precisamos fazer a sua instalação no settings do Django. Adicionar o nome 'eventex.core' no fim da Lista definida por INSTALLED_APPS (não esquecer da vírgula no final).

-- arquivo: eventex/settings.py
...
# Application definition
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'eventex.core',
]
...




14. Criar uma rota

O arquivo "eventex/urls.py" é a raiz de todas as rotas da aplicação. 

O Django oferece duas formas para se trabalhar com rotas: uma por caminhos (paths - forma nova e mais simples, e que será usada aqui), e a outra por regex (forma mais antiga).

Será adicionada uma rota vazia (''), que será a raiz do site, e será delegada para a view Home do core.

-- arquivo: eventex/urls.py
from django.contrib import admin
from django.urls import path
import eventex.core.views

urlpatterns = [
    path('', eventex.core.views.home),
    path('admin/', admin.site.urls),
]


Perceber que o PyCharm faz um alerta dizendo que não encontra a referência para "eventex.core.views.home"




15. Criar a view home

Será criada a primeira view, que neste momento nada mais é do que uma função.

-- arquivo: eventex/core/views.py
from django.shortcuts import render

def home(request):
    return render(request, 'index.html')



Qualquer View do Django é um objeto chamado, podendo ser uma classe, uma função ou uma instância. Recebe sempre como primeiro parâmetro uma instância de  HTTP Request, e responde uma instância HTTP Response.

O "render" funciona como um "atalho" para processar o "request" com um template, retornando uma instância de HTTP Response.


(*) Uma View do Django é um objeto "chamável" que recebe uma instância de HTTP Request e retorna um HTTP Response.



16. Criar o diretório templates dentro de core e criar o template index.html


-- arquivo: eventex/core/templates/index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Eventex</title>
</head>
<body>
    <h1>Welcome to the Eventex!</h1>
</body>
</html>




17. Rodando o sistema

No terminal:

$ manage runserver

Enquanto isso, no navegador: 
==> http://127.0.0.1:8000/
(OK)




== 1.26. A landing page ==
Vamos implementar a primeira versão do projeto com foco imediato no problema da nossa cliente.

Baixe o pacote landinpage.zip com o html e css preparados pelo nosso amigo designer.
https://welcometothedjango.com.br/wp-content/uploads/2015/12/landingpage.zip

Fazer a descompactação do arquivo landingpage.zip em ~/wttd

~/wttd/landingpage $ tree
.
├── css
│   ├── ...
├── fonts
│   ├── ...
├── img
│   ├── ...
├── index.html
└── js
    ├── ...





1. Criar o diretório static – onde ficam todos os arquivos estáticos

$ mkdir ~/wttd/eventex/core/static

2. Aplicar os arquivos recebidos do designer

$ mv ~/wttd/landingpage/css  ~/wttd/eventex/core/static
$ mv ~/wttd/landingpage/fonts  ~/wttd/eventex/core/static
$ mv ~/wttd/landingpage/img  ~/wttd/eventex/core/static
$ mv ~/wttd/landingpage/js  ~/wttd/eventex/core/static


3. Substituir o template html

$ mv ~/wttd/landingpage/index.html ~/wttd/eventex/core/templates/

$ rm -rf ~/wttd/landingpage


4. Rodar o projeto para ver como ficou

No terminal:

$ manage runserver

Enquanto isso, no navegador: 
==> http://127.0.0.1:8000/
(OK) : A página foi aberta sem nenhuma formatação, como era esperado.

Verificar no terminal se há alguma dica sobre o que está faltando.

|| May 14, 2020 - 14:50:17
|| Django version 3.0.6, using settings 'eventex.settings'
|| Starting development server at http://127.0.0.1:8000/
|| Quit the server with CONTROL-C.
|| [14/May/2020 14:50:29] "GET / HTTP/1.1" 200 6040
|| Not Found: /js/jquery-1.11.0.min.js
|| Not Found: /css/basalstyle/style.min.css
|| [14/May/2020 14:50:29] "GET /css/basalstyle/style.min.css HTTP/1.1" 404 2124
|| Not Found: /css/main.css
|| [14/May/2020 14:50:29] "GET /js/jquery-1.11.0.min.js HTTP/1.1" 404 2109
|| Not Found: /js/jquery.stellar.min.js
|| [14/May/2020 14:50:29] "GET /css/main.css HTTP/1.1" 404 2076
|| Not Found: /css/font-awesome.min.css
|| [14/May/2020 14:50:29] "GET /css/font-awesome.min.css HTTP/1.1" 404 2112
|| [14/May/2020 14:50:29] "GET /js/jquery.stellar.min.js HTTP/1.1" 404 2112
|| Not Found: /js/main.js
|| Not Found: /js/retina-1.1.0.min.js
|| [14/May/2020 14:50:29] "GET /js/retina-1.1.0.min.js HTTP/1.1" 404 2106
|| [14/May/2020 14:50:29] "GET /js/main.js HTTP/1.1" 404 2070
|| ...



5. Usar o recurso {% static %} do Django para localizar os arquivos estáticos

Aplicar o recurso do Django chamado "static". Isso é feito aplicando-se um "template tag" (chaves e porcentagem) na primeira linha do template.

-- arquivo: eventex/core/templates/index.html
{% load static %}
<!DOCTYPE html>
<html>
    <head>
...


Aplicar a template tag nos demais recursos:


-- arquivo: eventex/core/templates/index.html
...

        <!-- Favicon -->
        <link rel="shortcut icon" href="{% static 'img/favicon.ico' %}">
...        


6. Usar o recurso replace do PyCharm para fazer várias alterações no template

Fazer isso manualmente, item a item, é bem trabalhoso.

Para facilitar, foi feita uma substituição por expressão regular:

Regexp ==> (src|href)="((img|css|js).*?)"
Grupos ==> $1="{% static '$2' %}"

Foi aplicada a substituição em eventex/core/templates/index.html


Enquanto isso, no navegador: 
==> http://127.0.0.1:8000/
(OK) : Agora com toda a formatação desejada





== 1.27. Pronto é quando está no ar! ==

Prepare o seu ambiente de desenvolvimento com controle de versão para poder facilmente colocar o projeto no ar com apenas um comando.

1. Usar o heroku para fazer o deploy da aplicação

O deploy será feito no Heroku, um PAAS com todo o ambiente de deploy que precisamos. Pelo terminal, com apenas um comando conseguimos colocar o sistema no ar.

==> https://www.heroku.com/

Criar conta.



2. Instalar o heroku toolbelt (atualizado para The Heroku Command Line Interface (CLI))

==> https://devcenter.heroku.com/articles/heroku-cli#download-and-install

$ sudo snap install --classic heroku



3. Instalar o heroku toolbelt no Windows
(não foi necessário)



4. Checar se o heroku foi instalado

$ heroku



5. Fazer o login no heroku

$ heroku login




6. Checar a versão do heroku e seus plugins e a versão do git

$ heroku --version
heroku/7.41.1 linux-x64 node-v12.16.2

$ git --version
git version 2.17.1






== 1.28. O primeiro deploy a gente nunca esquece ==

Hora de fazer a primeira entrega e botar o sistema online na nuvem pra nossa cliente resolver seu problema.

Baixe o mapa da estrutura do Eventex.

1. A importância de separar os elementos da instância

Ex.: Dados de conexão ao BD, ambiente de serviços, etc.



2. Python decouple – permite ter um único código-fonte para várias instâncias



3. Instalar o python decouple 

Desenvolvido por Henrique Bastos

$ cd ~/wttd
$ pip install python-decouple


4. Configurar o arquivo settings.py e o arquivo .env

-- arquivo: eventex/settings.py
...

import os
from decouple import config
...


Com o "config", podemos configurar o SECRET_KEY, que é uma chave secreta usada pelo sistema de criptografia do Django para criar senhas seguras. 

Se qualquer pessoa fora do projeto conhecer esse SECRET_KEY, a segurança fica comprometida. Então o valor de SECRET_KEY não pode ser armazenado no código fonte. Por isso essa chave será extraída do settings.py.

O "config" lê as configurações da instância a partir das variáveis de ambiente do sistema.



-- arquivo: eventex/settings.py (linha removida) 
-- É óbvio que essa não é a minha chave
...
SECRET_KEY = 'sp-hfz%nk(4f(&a7e72z3tm!frb3)s0875597^2ufcuo3xo99'
...


-- arquivo: eventex/settings.py (linha nova)
...
SECRET_KEY = config('SECRET_KEY')
...



5. Configurar o SECRET_KEY

Criar um novo arquivo na raiz do workdir chamado ".env" e copoar o conteúdo da chave pra lá (sem aspas e sem espaços)

-- arquivo: ~/wttd/.env  (É óbvio que essa não é a minha chave)
SECRET_KEY=sp-hfz%nk(4f(&a7e72z3tm!frb3)s0875597^2ufcuo3xo99



Sempre que o projeto for iniciado e a função congig for executada, passando a chave SECRET_KEY como parâmetro, o "decouple" vai verificar se existe uma variável de ambiente com esse nome. Se não existir, o "decouple" verifica se o arquivo ".env" possui a configuração de um SECRET_KEY e carrega esse valor. Fica transparente.




6. Configurar o DEBUG

A mesma coisa será feita com o Debug, porque deixar DEBUG=True no código não é bacana.


-- arquivo: eventex/settings.py 
...
DEBUG = config('DEBUG')
...

Mas se não tivermos uma variável ambiente DEBUG definida no ambiente ou no arquivo ".env"? Dá pra deixar um valor padrão definido, que no nosso caso será False.

-- arquivo: eventex/settings.py 
...
DEBUG = config('DEBUG', default=False)
...

Detalhe: Tanto as variáveis de ambiente, como o conteúdo do arquivo ".env", estão no formato texto. O "config" vai carregar uma "string", e a string que representa False é uma string vazia. Qualquer outra "string", incluindo o texto "False" ou "True", são avaliados como verdadeiro.

Para isso a função "config" do "decouple" oferece uma opção chamada "cast", que converte o valor lido da variável de ambiente.

Será usado o construtor do Python chamado "bool", para instanciar booleanos. Dessa forma, como o "decouple" sabe que precisamos de um booleano, e já verifica se o texto é True, False, on, off, 1 ou 0.


-- arquivo: eventex/settings.py 
...
DEBUG = config('DEBUG', default=False, cast=bool)
...


-- arquivo: ~/wttd/.env 
SECRET_KEY=sp-hfz%nk(4f(&a7e68z3tm!frb3)s0q195597^2ufcuo3xohe
DEBUG = True





7. Configurar o banco de dados

Por padrão o DJango já configura o BD para ser um SQLite3, e configura inclusive o diretório em que esse BD será criado, usando o BASE_DIR como diretório.

-- arquivo: eventex/settings.py  (original)
...
# Database
# https://docs.djangoproject.com/en/3.0/ref/settings/#databases
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
...


O BASE_DIR é definido no início do arquivo settings.py, e serve para que não tenhamos caminhos hardcoded no código. Usar sempre caminhos relativos.

O SQLite3 será usado em desenvolvimento. Em produção será usado o PostgreSQL.

Para fazer essa distinção, será usado o "decouple" em conjunto com o "dj-database-url".




8. Instalar o dj-database-url

Consegue ler uma URL e extrair as informações necessárias para a configuração do BD no Django. O Django usa dicionários para configurar o BD, e o dj-database-url consegue mapear, a partir de uma URL, todos os parâmetros do dicionário.

-- Instalação
$ pip install dj-database-url


-- arquivo: eventex/settings.py 
...
import os
from decouple import config
from dj_database_url import parse as dburl
...


O "dj_database_url" tem também a capacidade de ler a variável de ambiente DATABASE_URL, mas estamos interessados na capacidade de fazer o parser de URLs e criar os dicionários de configuração do BD do Django.

Não faremos o apontamento direto para o nome do arquivo, como feito na configuração padrão. Transformaremos em uma URL.


-- arquivo: eventex/settings.py  (modificação)
...
# Database
# https://docs.djangoproject.com/en/3.0/ref/settings/#databases
default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')
DATABASES = {
    'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
}
...


Quando o settings.py for carregado, o "config" do "decouple" será executado e buscará a variável de ambiente DATABASE_URL, se não encontrar, procurará no arquivo ".env", e se mesmo assim não encontrar, usará o valor definido pela variável "default_dburl" (que será usado para desenvolvimento), e passará o valor encontrado para a função "dburl", que foi importada do "dj_database_url", que é capaz de expandir a URL para dicionários de configuração do DJango.

Quando o projeto for executado sem nenhuma variável de ambiente, sem nada preparado, o SQLite será usado.




9. Configurar "hosts permitidos" e arquivos estáticos

-- arquivo: eventex/settings.py  (original)
...
ALLOWED_HOSTS = []
...

Quando a aplicação recebe uma requisição HTTP, a requisição vem endereçada a um host específico. O Heroku exige que seja informado por qual host a aplicação vai responder.

Neste momento, para atender minimamente o requisito do Heroku, informaremos que a aplicação responde a qualquer reuisição de qualquer host. Depois essa configuração será ajustada

-- arquivo: eventex/settings.py  (modificado)
...
ALLOWED_HOSTS = ['*']
...


Próxima etapa: Configurar os arquivos estáticos

-- arquivo: eventex/settings.py  (modificado)
...
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/3.0/howto/static-files/
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
...


É para o diretório definido por STATIC_ROOT que o Django vai copiar todos os arquivos estáticos que encontrar nas dependências do projeto.

Atualmente os arquivos estáticos estão todos em "eventex/core/static/". Em um projeto simples, os arquivos estáticos até ficam na mesma máquina que a da aplicação, mas não podemos assumir isso. Podemos hospedar os nossos "statics" na Amazon ou em uma CDN qualquer, e o Django suporta todas essas formas de distribuir os estáticos.

Por padrão, o Django não serve arquivos estáticos, porque isso é ineficiente. O arquivo estático não tem nada de dinâmico que justifique passar por todo o processo do Django. Então, o ideal é configurar um servidor web para servir diretamente esses arquivos (no nosso caso, no diretório "staticfiles" que estará abaixo do diretório "wttd").

No Heroku não temos acesso a essas configurações.






10. Instalar dj-static

Para contornar o fato ddo Django não servir arquivos estáticos por padrão, será usado o "dj-static", que basicamente é uma aplicação "wsgi" do padrão do Python, que fará frente ao Django com a missão de servir os arquivos estáticos.

-- instalação
$ pip install dj-static




11. Configurar o arquivo wsgi.py

-- arquivo: eventex/wsgi.py (original)
import os
from django.core.wsgi import get_wsgi_application
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'eventex.settings')
application = get_wsgi_application()


-- arquivo: eventex/wsgi.py (modificado)
import os
from dj_static import Cling
from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'eventex.settings')

application = Cling(get_wsgi_application())



O Cling é uma aplicação "wsgi", assim como o "get_wsgi_application" (do Django)  retorna uma aplicação "wsgi". E podemos compor esses componentes, como foi feito na modificação anterior.

O que está sendo feito é: Quando o servidor web passar alguma requisição, vai passar primeiro pelo "Cling", e quando este terminar o seu trabalho, delegará a chamada para o "get_wsgi_application" (do Django).

Dessa forma conseguimos servir os arquivos estáticos antes de chegar a requisição do Django.




12. Registrar as dependências do projeto

$ pip freeze
asgiref==3.2.7
dj-database-url==0.5.0
dj-static==0.0.6
Django==3.0.6
python-decouple==3.3
pytz==2020.1
sqlparse==0.3.1
static3==0.7.0

Este comando mostra todas as bibliotecas que estão instaladas no VirtualEnv. Teremos que inserir esses itens em um arquivo texto especial.


$ pip freeze > requirements.txt


$ cat requirements.txt 
asgiref==3.2.7
dj-database-url==0.5.0
dj-static==0.0.6
Django==3.0.6
python-decouple==3.3
pytz==2020.1
sqlparse==0.3.1
static3==0.7.0


13. Adicionar no requirements.txt alguns elementos necessários para o heroku


O Heroku precisa de algumas coisas adicionais.

-- arquivo: ~/wttd/requirements.txt
asgiref==3.2.7
dj-database-url==0.5.0
dj-static==0.0.6
Django==3.0.6
python-decouple==3.3
pytz==2020.1
sqlparse==0.3.1
static3==0.7.0
gunicorn==19.8.1
psycopg2==2.7.4


O "gunicorn" é um servidor web que receberá as requisições e as retransmitirá para o Django.

O "psycopg2" é o driver do BD PostgreSQL, que é o usado no Heroku.

A adição desses dois itens não foi feita com o "pip install" porque o S.O. pode precisar de outras dependências externas para conseguir instalar esses itens, e isso pode ser um pouco complicado. Como não os usaremos em desenvolvimento, pois o nosso desenvolvimento será com o servidor web do Django e o BD SQLite, fazemos apenas a referência desse sitens adicionais, porque o Heroku fará a leitura desse arquivo para instalar todas as dependências e colocar a nossa aplicação no ar.





14. Criar o arquivo Procfile

O "Procfile" é um arquivo especial do Heroku, para que ele saber como inicializar a nossa aplicação.

-- arquivo: ~/wttd/Procfile
web: gunicorn eventex.wsgi --log-file -

O que está sendo solicitado: Inicie um processo web, que começará com um comando "gunicorn", que fará o start da aplicação "eventex.wsgi" (que é o nosso projeto Django). Os demais parâmetros são referentes ao log do gunicorn, e como será conectado ao log do Heroku.





15. Montar o repositório git

$ git init

Deixar no repositório apenas o que é fonte.




16. Explicação sobre quais arquivos não devem ir para o repositório git

Dispensar: 
- .env (cada desenvolvedor terá o seu)
- .idea/
- .wttd/
- db.sqlite3
- os arquivos/diretórios de "cache" do Python (vide "ls eventex")



17. Criar o arquivo .gitignore (pode instalar o plugin no PyCharm)

-- arquivo: ~/wttd/.gitignore
.env
.idea
.wttd
*.sqlite3
*pyc
__pycache__


18. Adiciona o arquivo .gitignore no repositório git

$ git add .gitignore

$ git commit -m "Initial commit"



19. Adiciona os outros arquivos no repositório git

$ git add .

$ git commit -m "Import project"




20. Criar uma app no heroku para poder colocar no ar (utilizar o seu nome)

Srá usado o client do Heroku para criar o ambiente "produtivo" e enviar a aplicação pra lá.

$ heroku apps:create eventex-zanatabr
Creating ⬢ eventex-zanatabr... done
https://eventex-zanatabr.herokuapp.com/ | https://git.heroku.com/eventex-zanatabr.git


Fazer duas verificações pra saber se funcionou:

1 - ver se o repositório git reconhece o endereço do Heroku

$ git remote -v
heroku  https://git.heroku.com/eventex-zanatabr.git (fetch)
heroku  https://git.heroku.com/eventex-zanatabr.git (push)

Perceber que foi criado um "remote" chamado "heroku".

Toda a comunicação do projeto para enviar o código para o Heroku é feito através do Git. Importante verificar se está tudo Ok.

2 - Abrir a aplicação e ver se está tudo Ok no Heroku.

$ heroku open

Um navegador deverá ser aberto com uma página de boas-vindas (Heroku | Welcome to your new app!).




21. Fazer o deploy no heroku

Configurar o ambiente de produção com as variáveis de ambiente do projeto.

$ cat .env
SECRET_KEY=sp-hfz%nk(4f(&a7e72z3tm!frb3)s0875597^2ufcuo3xo99
DEBUG=True

Para configurar o ambiente do Heroku:

$ heroku config:set SECRET_KEY='sp-hfz%nk(4f(&a7e72z3tm!frb3)s0875597^2ufcuo3xo99'

Setting SECRET_KEY and restarting ⬢ eventex-zanatabr... done, v3
SECRET_KEY: sp-hfz%nk(4f(&a7e72z3tm!frb3)s0875597^2ufcuo3xo99

$ heroku config:set DEBUG=True
Setting DEBUG and restarting ⬢ eventex-zanatabr... done, v4
DEBUG: True


Enviar o projeto para o Heroku:

$ git push heroku master --force

Counting objects: 94, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (88/88), done.
Writing objects: 100% (94/94), 1.66 MiB | 185.00 KiB/s, done.
Total 94 (delta 1), reused 0 (delta 0)
remote: Compressing source files... done.
remote: Building source:
remote: 
remote: -----> Python app detected
remote: -----> Installing python-3.6.10
remote: -----> Installing pip
remote: -----> Installing SQLite3
remote: -----> Installing requirements with pip
remote:        Collecting asgiref==3.2.7
remote:          Downloading asgiref-3.2.7-py2.py3-none-any.whl (19 kB)
remote:        Collecting dj-database-url==0.5.0
remote:          Downloading dj_database_url-0.5.0-py2.py3-none-any.whl (5.5 kB)
remote:        Collecting dj-static==0.0.6
remote:          Downloading dj-static-0.0.6.tar.gz (3.4 kB)
remote:        Collecting Django==3.0.6
remote:          Downloading Django-3.0.6-py3-none-any.whl (7.5 MB)
remote:        Collecting python-decouple==3.3
remote:          Downloading python-decouple-3.3.tar.gz (10 kB)
remote:        Collecting pytz==2020.1
remote:          Downloading pytz-2020.1-py2.py3-none-any.whl (510 kB)
remote:        Collecting sqlparse==0.3.1
remote:          Downloading sqlparse-0.3.1-py2.py3-none-any.whl (40 kB)
remote:        Collecting static3==0.7.0
remote:          Downloading static3-0.7.0.tar.gz (24 kB)
remote:        Building wheels for collected packages: dj-static, python-decouple, static3
remote:          Building wheel for dj-static (setup.py): started
remote:          Building wheel for dj-static (setup.py): finished with status 'done'
remote:          Created wheel for dj-static: filename=dj_static-0.0.6-py3-none-any.whl size=3051 sha256=f478f644b0c18f7933927da8ae3ec5f1745ee2e6f7795aac0c3ff25ae5d4faa3
remote:          Stored in directory: /tmp/pip-ephem-wheel-cache-068aigar/wheels/10/00/d5/99c5c6c018f7696d8dae6f9da967be89e8796ca2cd31a54cb6
remote:          Building wheel for python-decouple (setup.py): started
remote:          Building wheel for python-decouple (setup.py): finished with status 'done'
remote:          Created wheel for python-decouple: filename=python_decouple-3.3-py3-none-any.whl size=9024 sha256=e0e914c263e62f9842c94409acc884c7936665192f8d1af5b4e656272a05ba95
remote:          Stored in directory: /tmp/pip-ephem-wheel-cache-068aigar/wheels/5a/3b/c7/bbce6da66add721a4b784a84db945145a05ac16cdee73d8422
remote:          Building wheel for static3 (setup.py): started
remote:          Building wheel for static3 (setup.py): finished with status 'done'
remote:          Created wheel for static3: filename=static3-0.7.0-py3-none-any.whl size=18606 sha256=86a9490361c0ae894ffc16728fd4e4a1b7d090e1d9b6142f898988189c614c09
remote:          Stored in directory: /tmp/pip-ephem-wheel-cache-068aigar/wheels/dd/01/9e/01e8b51c546e5d95b79bc530713900fb2eb5530d5af78efe39
remote:        Successfully built dj-static python-decouple static3
remote:        Installing collected packages: asgiref, dj-database-url, static3, dj-static, pytz, sqlparse, Django, python-decouple
remote:        Successfully installed Django-3.0.6 asgiref-3.2.7 dj-database-url-0.5.0 dj-static-0.0.6 python-decouple-3.3 pytz-2020.1 sqlparse-0.3.1 static3-0.7.0
remote: -----> $ python manage.py collectstatic --noinput
remote:        193 static files copied to '/tmp/build_7b30c150429259731011e23090b1671b/staticfiles'.
remote: 
remote: -----> Discovering process types
remote:        Procfile declares types -> web
remote: 
remote: -----> Compressing...
remote:        Done: 54.2M
remote: -----> Launching...
remote:        Released v7
remote:        https://eventex-zanatabr.herokuapp.com/ deployed to Heroku
remote: 
remote: Verifying deploy... done.
To https://git.heroku.com/eventex-zanatabr.git
 * [new branch]      master -> master



Abrir a aplicação e ver se está tudo Ok no Heroku.

$ heroku open






== 1.39. Visão além do alcance ==

Exploda a sua mente com uma detalhada análise sobre tudo que você viu até aqui relacionando código, técnicas, táticas, estratégias, negócios e autonomia.

Faça download do mapa mental em XMind.

[MINDSET]
- Use o modo interativo para experimentar ciclos de feedback curto
- Navegue no ecossistema com o iPython
- Foco no negócio da cliente
- Prazo o cliente define. Qualidade é intrínseca ao seu trabalho. Escopo é que se negocia.

[LIVE SOFTWARE]
- Projeto embrionário
- Execução de ponta a ponta
- Decida apenas o necessário
- O fluxo revela a estrutura
- A realidade revela a necessidade

[PROGRAME COMO UM CHEF]
- O layout do projeto
- Aliases e comandos auxiliares
- Se tá difícil, tá errado!

[AUTONOMATION]
- Setup com 1 comando

[PRONTO É QUANDO ESTÁ NO AR]
- 1 código para N instâncias – The Twelve-Factor App
- Deploy tem risco arquitetural / simplicidade do deploy impacta a frequência
- O heroku é barato

[A ARMA SECRETA DO TONY STARK]
- O poder por trás da sintaxe simples
- Alto nível além dos bits e bytes
- Conceitos e abstrações poderosos
- Açúcar sintático adoça a vida
- O Zen do Python está presente no código / o poder das baterias






== 1.30. Mão na massa! ==

Complemente sua caminhada com um conjunto de exercícios sensacionais que te ajudarão à praticar e assimilar os conceitos aprendidos sobre a linguagem.

Faça download dos arquivos de exercício.
google-python-exercises.zip

- string1
- list1
- string2
- list2
- mimic
