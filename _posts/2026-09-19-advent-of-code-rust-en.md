---
title: Aprendendo Rust com Advent of Code
slug: advent-of-rust

layout: post
published: false

lang: pt-BR
permalink: /posts/:slug
page_id: advent-of-rust
---

Faz algum tempo que venho sentido saudade de programar
coisas na mão.

Sei que não é eficiente de verdade e talvez não muito produtivo
hoje em dia, mas veja bem caro leitor, se tudo nessa vida fosse sobre produtividade
a maioria das empresas multi-bilionárias que se dizem "produtivas" não fariam tanto
dinheiro assim. Ora quem iria ficar horas vendo reels no instagram!? que loucura.

De qualquer forma, o objetivo desse artigo não é criticar um modelo claramente de
sucesso e sim só falar de como venho tentando satisfazer essa vontade de programar
novamente coisas na mão.

O "fix" é simples, programando coisas novamente na mão...

## Na época que eu programava em C

Minha primeira liguagem de programação foi C, então sempre tive certo apreço por
linguagens consideradas "baixo nível", existe algo sobre precisar criar até a
própria implementação de uma estrutura de dados que programar em, por exemplo,
python, não consegue entregar.

Mas sinto que os meus dias de programar em C já passaram, a última coisa que eu fiz em C,
[bmount](https://github.com/Marce1in/bmount), me mostrou que talvez não seja a minha
praia ficar pensando em como reservar e limpar memória ao processar um input
e acabar necessitando de Arrays de chars de 4 dimensões (pointeiro de um ponteiro de um ponteiro) para só mostrar
uma resposta bonitinha para o usuário.

```
void parse_string(char *input, int *index, char ***parsed_strings, size_t* size){

    const int start = *index + 1;
    const int rows = lines_counter(input, index);

    const int end = *index + 1;
    const int columns = sections_counter(input, start + 1);

    char** tmp = (char**)realloc(*parsed_strings, (*size + (rows * 3)) * sizeof(char**));
    if (tmp == NULL){
        longjmp(savebuf, MEM_ERROR);
    }

    *parsed_strings = tmp;

    for (int i = start, current_column = 0, first_row = true; i < end; i++){

        if (input[i] == ' ' && input[i + 1] != ' '){
            current_column++;
        }
        else if (input[i] == '\n'){
            current_column = 0;
            first_row = false;
            continue;
        }

        //This "size <= 3" makes the program take only the first header and ignore the other headers
        if (first_row == false || *size <= 3 ){

            if (current_column == 0 && input[i] != ' '){

                extract_string(parsed_strings, size, input, &i, ' ', 32);
            }
            else if (current_column == columns - 1 && input[i] != ' '){

                extract_string(parsed_strings, size, input, &i, ' ', 32);
            }
            else if (current_column == columns && input[i] != ' '){

                extract_string(parsed_strings, size, input, &i, '\n', 64);
            }
        }
    }
}
```

> sério, eu usei até jump como estratégia de limpeza de memória caso um erro
> acontecesse, o que eu achei genial na época.

## Enfim o rust

Eu sempre quis aprender rust, parece uma linguagem baixo nível moderninha com
a documentação bem organizada e de fácil acesso.

O modelo de alocação de memória dele também parece ser diferente de C, usando o conceito
de "borrow checker" em vez do clássico `malloc()` e depois `free()`.

Então dada a vontade de voltar a programar na mão unida com a vontade de aprender rust
nasceu então esse artigo.

## Como esse artigo vai funcionar

Eu sempre gosto de resolver problemas do [Advent Of Code](https://adventofcode.com/) para
aprender uma linguagem nova, os problemas de lá geralmente te forçam a usar todas as coisas
que a lib padrão de uma linguaguem tende a oferecer.

Apartir daqui eu não sei muito bem o que esse artigo vai se tornar, eu percebi que gosto
de escrever sobre as coisas, geralmente explicar elas também me ajuda a entender as mesmas melhor.

Então esse artigo agora vai se tornar muito mais um amontuado de ideias e conceitos
que eu vou aprendendo sobre rust enquanto resolvo os problemas de 2025 do advent of code.

A edição de 2025 tem 12 dias com 2 problemas cada, com cada dia aumentando a dificuldade. Vou separando por subtítulos as soluções...

### Dia 1

#### Problema 1

Começando pelo problema, ele se parece um sistema similar de como aquelas
fechaduras de cofre funcionam.

basicamente eu tenho um arquivo com instruções:

```
L68
L30
R48
L5
R60
L55
L1
L99
R14
L82
```

A gente começa apontando para o número 50 (imagine que podemos caminhar entre 0 a 99),
`L68` significa que eu preciso andar 68 casas para a esquerda, logo começando do 50
é 50 - 68 = -18, todavia existe uma pegadinha, não pode existir números negativos e nem acima de 99

assim se tivermos, por exemplo, L1 e estivermos já no 0, o número se torna 99

então acho que dá para resolver tipo: 50 - 68 = -18, e daí a gente faz -18 * -1 = 18 e aí finalmente
99 - 18 nos dá até onde o número andou ao ir para esquerda

para direita é a mesma lógica só que a o contrário.

por fim, o que o problema pede como resposta é a **quantidade de vezes que seguindo a sequência nós paramos no número zero**

o problema deixa isto meu claro no exemplo:

```
Following these rotations would cause the dial to move as follows:

The dial starts by pointing at 50.
The dial is rotated L68 to point at 82.
The dial is rotated L30 to point at 52.
The dial is rotated R48 to point at 0.
The dial is rotated L5 to point at 95.
The dial is rotated R60 to point at 55.
The dial is rotated L55 to point at 0.
The dial is rotated L1 to point at 99.
The dial is rotated L99 to point at 0.
The dial is rotated R14 to point at 14.
The dial is rotated L82 to point at 32.

Because the dial points at 0 a total of three times during this process, the password in this example is 3.
```

então além de seguir todo o processo a gente precisa também ter alguma variável que vai contando caso apereça zero.

acho que vai ser tranquilo.

vamos lá, primeiro um arquivo em rust usa a extensão `.rs` e como C a gente inicia com uma main:

```
fn main() {
    println!("Hello World!");
}
```

`println!()` parece ser o "printf" deles, me pergunto para qual motivo tem uma exclamação...

para compilar é fácil, só instalar rust e rodar `rustc {nome-do-arquivo}.rs`, nisso o programa
compila e depois é só executar com `./{nome-do-arquivo}`

bom para resolver o problema primeiro eu preciso parsear aquele exemplo que eu recebi, todos
os problemas do advent of code vem em formato de arquivo e escrever parsers para cada um é normal.





#### Problema 2


