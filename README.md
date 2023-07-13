# trabalhofinalpesqordenacao
Trabalho final de Pesquisa e Ordenação - Maria Eduarda K.G
from Gerador import Gerador


class Pessoa:
    def __init__(self, nome, sobrenome):
        self.nome = nome
        self.sobrenome = sobrenome


# PESQUISA EM ÁRVORE DE BUSCA BINÁRIA
class No:
    def __init__(self, pessoa):
        self.pessoa = pessoa
        self.esquerda = None
        self.direita = None


def inregistro(raiz, pessoa):
    if raiz is None:
        return No(pessoa)
    if pessoa.sobrenome < raiz.pessoa.sobrenome:
        raiz.esquerda = inregistro(raiz.esquerda, pessoa)
    elif pessoa.sobrenome > raiz.pessoa.sobrenome:
        raiz.direita = inregistro(raiz.direita, pessoa)
    return raiz


def buscar_registro(raiz, sobrenome):
    if raiz is None or sobrenome == raiz.pessoa.sobrenome:
        return raiz
    if sobrenome < raiz.pessoa.sobrenome:
        return buscar_registro(raiz.esquerda, sobrenome)
    return buscar_registro(raiz.direita, sobrenome)


# Criação da árvore de busca binária
arvore = None

# Inserção dos registros na árvore de busca binária
for i in range(len(Gerador.nomes)):
    pessoa = Pessoa(Gerador.nomes[i], Gerador.sobrenomes[i])
    arvore = inregistro(arvore, pessoa)


# PESQUISA COM JUMP SEARCH
def jump_search(lista, val):
    tam = len(lista)
    salto = int(tam ** 0.5)  # Tamanho salto
    anterior = 0
    while lista[min(salto, tam) - 1] < val:
        anterior = salto
        salto += int(tam ** 0.5)
        if anterior >= tam:
            return -1
    while lista[anterior] < val:
        anterior += 1
        if anterior == min(salto, tam):
            return -1
    if lista[anterior] == val:
        return anterior
    return -1


reg = []  # lista de registros pra pôR nomes e sobrenomes

# Colocar na lista de registros
for i in range(len(Gerador.nomes)):
    nomecompleto = Gerador.nomes[i] + ' ' + Gerador.sobrenomes[i]
    reg.append(nomecompleto)

reg.sort()  # pra ordenar - como vimos na apresentação do trabalho de 1pto existe em python o .sort, que usa internamente algoritmo de ordenação

# PESQUISA EM TABELA DE DISPERSÃO LINEAR
tamtabela = 1000  # Tam da tabela de dispersão

#Para calcular a função de hash
def funcaohash(chave):
    # Usa conforme os caracteres o seu equivalente na tabela ascii, para transformar em números
    somaascii = sum(ord(char) for char in chave)
    return somaascii % tamtabela


# PESQUISA EM TABELA DE DISPERSÃO LINEAR
tabeladispersao = {}

# Colocando na tabela de dispersão
for i in range(len(Gerador.nomes)):
    pessoa = Pessoa(Gerador.nomes[i], Gerador.sobrenomes[i])
    chave = pessoa.sobrenome
    indice = funcaohash(chave)
    while indice in tabeladispersao:
        indice = (indice + 1) % tamtabela
    tabeladispersao[indice] = pessoa

# Pesquisar
def pesquisar_registro(sobrenome):
    chave = sobrenome
    indice = funcaohash(chave)
    while indice in tabeladispersao:
        pessoa = tabeladispersao[indice]
        if pessoa.sobrenome == sobrenome:
            return pessoa
        indice = (indice + 1) % tamtabela
    return None

# PESQUISA POR ÁRVORE AVL
class NoAVL:  # classe para o nó
    def __init__(self, chave):
        self.chave = chave
        self.esquerda = None
        self.direita = None
        self.altura = 1

# Para altura de um nó da árvore
def altura(no):
    if no is None:
        return 0
    return no.altura

# Para o fator de balanceamento de um nó
def fatorbalanceamento(no):
    if no is None:
        return 0
    return altura(no.esquerda)-altura(no.direita)

# Rotação simples à esquerda em um nó
def roesquerda(no):
    novaraiz = no.direita
    no.direita = novaraiz.esquerda
    novaraiz.esquerda = no
    no.altura = max(altura(no.esquerda), altura(no.direita))+1
    novaraiz.altura = max(altura(novaraiz.esquerda),
                          altura(novaraiz.direita))+1
    return novaraiz

# Rotação simples à direita
def rodireita(no):
    if no is None or no.esquerda is None:
        return no
    novaraiz = no.esquerda
    no.esquerda = novaraiz.direita
    novaraiz.direita = no
    no.altura = max(altura(no.esquerda), altura(no.direita))+1
    novaraiz.altura = max(altura(novaraiz.esquerda),
                          altura(novaraiz.direita))+1
    return novaraiz

# Para inserir um nó na árvore
def inserirno(raiz, chave):
    if raiz is None:
        return NoAVL(chave)
    if chave.sobrenome < raiz.chave.sobrenome:
        raiz.esquerda = inserirno(raiz.esquerda, chave)
    elif chave.sobrenome > raiz.chave.sobrenome:
        raiz.direita = inserirno(raiz.direita, chave)
    else:
        if chave.nome < raiz.chave.nome:
            raiz.esquerda = inserirno(raiz.esquerda, chave)
        else:
            raiz.direita = inserirno(raiz.direita, chave)
    raiz.altura = max(altura(raiz.esquerda), altura(raiz.direita))+1
    fator = fatorbalanceamento(raiz)
    if fator > 1:
        if chave.sobrenome < raiz.esquerda.chave.sobrenome:
            return rodireita(raiz)
        elif chave.sobrenome > raiz.esquerda.chave.sobrenome:
            raiz.esquerda = roesquerda(raiz.esquerda)
            return rodireita(raiz)
        else:
            if chave.nome < raiz.esquerda.chave.nome:
                return rodireita(raiz)
            else:
                raiz.esquerda = roesquerda(raiz.esquerda)
                return rodireita(raiz)
    if fator < -1:
        if chave.sobrenome > raiz.direita.chave.sobrenome:
            return roesquerda(raiz)
        elif chave.sobrenome < raiz.direita.chave.sobrenome:
            raiz.direita = rodireita(raiz.direita)
            return roesquerda(raiz)
        else:
            if chave.nome > raiz.direita.chave.nome:
                return roesquerda(raiz)
            else:
                raiz.direita = rodireita(raiz.direita)
                return roesquerda(raiz)
    return raiz

# Pesquisar
def pesquisar(raiz, sobrenome, nome):
    if raiz is None:
        return None
    if raiz.chave.sobrenome == sobrenome and raiz.chave.nome == nome:
        return raiz.chave
    if raiz.chave.sobrenome > sobrenome:
        return pesquisar(raiz.esquerda, sobrenome, nome)
    elif raiz.chave.sobrenome < sobrenome:
        return pesquisar(raiz.direita, sobrenome, nome)
    else:
        if raiz.chave.nome > nome:
            return pesquisar(raiz.esquerda, sobrenome, nome)
        else:
            return pesquisar(raiz.direita, sobrenome, nome)


# Criação da árvore
arvoreavl = None

# Colocando na árvore
for i in range(len(Gerador.nomes)):
    pessoa = Pessoa(Gerador.nomes[i], Gerador.sobrenomes[i])
    arvoreavl = inserirno(arvoreavl, pessoa)


while True:
    print("\nMenu para escolher o tipo de pesquisa que deseja fazer:")
    print("1 - Com Árvore de Busca Binária: ")
    print("2 - Com Jump Search: ")
    print("3 - Em Tabela de Dispersão Linear: ")
    print("4 - Por Árvore AVL: ")
    print("0 - Sair: ")

    opcao = int(input("Escolha uma opção: "))
    if opcao == 1:
        pesquisa = input("Digite o sobrenome da pessoa que deseja pesquisar: ")
        registro_encontrado = buscar_registro(arvore, pesquisa)
        if registro_encontrado:
            print(
                f'Com esse sobrenome foi encontrado (a): {registro_encontrado.pessoa.nome} {registro_encontrado.pessoa.sobrenome}')
        else:
            print('Sobrenome não encontrado.')
    elif opcao == 2:
        nome_pesquisa = input(
            "Digite o nome e sobrenome da pessoa que deseja pesquisar: ")
        encontrado = jump_search(reg, nome_pesquisa)
        if encontrado != -1:
            # como ordena por nome completo, não consigo pesquisar só por nome e trazer, por exemplo, o primeiro nome e o primeiro sobrenome do vetor nome e vetor sobrenome do arquivo Gerador. Se ordenasse só por nome não traria o sobrenome correto, conforme tá no Gerador
            print(
                f'O nome pesquisado: {nome_pesquisa} foi encontrado no índice da lista de registros {encontrado}.')
        else:
            print('O nome pesquisado não foi encontrado.')
    elif opcao == 3:
        sobrenome_pesquisa = input(
            "Digite o sobrenome da pessoa que deseja pesquisar: ")
        registro_encontrado = pesquisar_registro(sobrenome_pesquisa)
        if registro_encontrado:
            print(
                f'Com esse sobrenome foi encontrado o(a): {registro_encontrado.nome} {registro_encontrado.sobrenome}')
        else:
            print('Não foi encontrado pessoa com esse sobrenome.')
    elif opcao == 4:
        nome_pesquisa = input("Digite o nome da pessoa que deseja pesquisar: ")
        sobrenome_pesquisa = input(
            "Digite o sobrenome da pessoa que deseja pesquisar: ")
        registro_encontrado = pesquisar(
            arvoreavl, sobrenome_pesquisa, nome_pesquisa)
        if registro_encontrado:
            print(
                f'Foi encontrado o (a): {registro_encontrado.nome} {registro_encontrado.sobrenome}')
        else:
            print('Não foi encontrado pessoa com esse sobrenome.')
    elif opcao == 0:
        print("Saindo...")
        break
    else:
        print("Opção inválida.")
