# Comparação e Ordenação de objetos em Java.

Para ordenar coleções de objetos em java, temos 2 opções:
- Fazer o objeto implementar a interface `Comparable`.
- Fazer o uso de um outro objeto que implementa a interface `Comparator`.

## Interface Comparable
Comparable é uma interface que tem um parametro de classe `T`. Um objeto que implementa essa interface deve implementar o metódo `int compareTo(T)`. Esse metódo deve retornar um número negativo no caso do primeiro objeto ser menor que o segund, positivo caso seja maior, e 0 caso sejam iguais. 
Ou seja, se `a.compareTo(b) < 0`, então `a < b`, se `a.compareTo(b) > 0`, `a > b`, e se `a.compareTo(b) = 0`, então `a = b`.
Para um exemplo, definimos a classe Pessoa:
```java
public class Pessoa implements Comparable<Pessoa> {
    String nome;
    int idade;
    int altura;
    public Pessoa(String nome, int idade, int altura) {
        this.nome = nome;
        this.idade = idade;
        this.altura = altura;
    }

    public void print() {
        System.out.println(nome + ". " + idade + " anos. " + altura + "cm.");
    }
    
    @Override
    public int compareTo(Pessoa outraPessoa) {
        return nome.compareTo(outraPessoa.nome);
    }
}
```
Essa classe implementa a interface `Comparable<Pessoa>`. Ou seja, podemos comparar um objeto da classe Pessoa a outro objeto da classe Pessoa.
O metódo `compareTo` é definido como
```java
@Override
public int compareTo(Pessoa outraPessoa) {
    return nome.compareTo(outraPessoa.nome);
}
```
Ou seja, ordenamos as pessoas em ordem alfabética de seus nomes.
Se criamos uma lista de pessoas como
```java
ArrayList<Pessoa> pessoas = new ArrayList<Pessoa>();
pessoas.add(new Pessoa("Denise", 23, 180));
pessoas.add(new Pessoa("Bob", 28, 172));
pessoas.add(new Pessoa("Alice", 25, 173));
pessoas.add(new Pessoa("Charles", 27, 170));
```
Podemos imprimir para o output todas as pessoas com
```java
for (Pessoa p : pessoas) {
    p.print();
}
```
Resultando em
```
Denise. 23 anos. 180cm.
Bob. 28 anos. 172cm.
Alice. 25 anos. 173cm.
Charles. 27 anos. 170cm.
```
Mas e se queremos que essa lista de pessoas esteja ordenada de forma alfabética, como definimos ao implementar a interface `Comparable`?
Podemos ordenar ArrayLists, ou outras coleções, com o metódo static `sort` da classe `Collections`
```java
Collections.sort(pessoas);
for (Pessoa p : pessoas) {
    p.print();
}
```
Resultando em
```
Alice. 25 anos. 173cm.
Bob. 28 anos. 172cm.
Charles. 27 anos. 170cm.
Denise. 23 anos. 180cm.
```
Se queremos apenas passar por cada pessoa em ordem alfabética, sem necessariamente re-ordenar a lista, podemos utilizar `streams` com expressões lambda.
(Observe a similaridade com funções de alta ordem em linguagens funcionais, como Haskell)
```java
pessoas.stream().forEach(p -> p.print());
```
Resulta em
```
Denise. 23 anos. 180cm.
Bob. 28 anos. 172cm.
Alice. 25 anos. 173cm.
Charles. 27 anos. 170cm.
```
Ordenando:
```java
pessoas.stream().sorted().forEach(p -> p.print());
```
```
Alice. 25 anos. 173cm.
Bob. 28 anos. 172cm.
Charles. 27 anos. 170cm.
Denise. 23 anos. 180cm.
```
Porém a lista original continua na ordem na qual ela foi criada.

## Objeto Comparator
Mas e se quisermos ordenar objetos de várias formas diferentes, ou se não pudermos implementar a interface `Comparable`?
Podemos então passar para os metódos `sort` um objeto que implementa a interface `Comparator`. Essa interface possuí um metódo que recebe dois objetos e os compara como no metódo `compareTo` acima.
Esse objeto pode ser criado através de uma expressão lambda para ser passado mais convenientemente para os `sorts`

```java
pessoas.stream().sorted((p1, p2) -> p1.nome.compareTo(p2.nome)).forEach(p -> p.print());
```
resultando em
```
Alice. 25 anos. 173cm.
Bob. 28 anos. 172cm.
Charles. 27 anos. 170cm.
Denise. 23 anos. 180cm.
```
Aqui,  `(p1, p2) -> p1.nome.compareTo(p2.nome)` é a expressão lambda que retorna o objeto Comparator.
Podemos então ordernar ou passar pela lista em ordens diferentes:
```java
pessoas.stream().sorted((p1, p2) -> p1.nome.compareTo(p2.nome)).forEach(p -> p.print());
pessoas.stream().sorted((p1, p2) -> p1.idade - p2.idade).forEach(p -> p.print());
pessoas.stream().sorted((p1, p2) -> p1.altura - p2.altura).forEach(p -> p.print());
```
Imprime todas as pessoas da lista, primeiro em ordem alfabética de seus nomes, seguido pela ordem de idade, e então pela ordem de altura
```
Alice. 25 anos. 173cm.
Bob. 28 anos. 172cm.
Charles. 27 anos. 170cm.
Denise. 23 anos. 180cm.
Denise. 23 anos. 180cm.
Alice. 25 anos. 173cm.
Charles. 27 anos. 170cm.
Bob. 28 anos. 172cm.
Charles. 27 anos. 170cm.
Bob. 28 anos. 172cm.
Alice. 25 anos. 173cm.
Denise. 23 anos. 180cm.
```
Podemos também passar essas expressões lambda para `Collections.sort()`, ordenando a lista em si:
```java
for (Pessoa p : pessoas) {
    p.print();
}
Collections.sort(pessoas, (p1, p2) -> p1.altura - p2.altura);
for (Pessoa p : pessoas) {
    p.print();
}
``` 
Resulta em:
```
Denise. 23 anos. 180cm.
Bob. 28 anos. 172cm.
Alice. 25 anos. 173cm.
Charles. 27 anos. 170cm.
Charles. 27 anos. 170cm.
Bob. 28 anos. 172cm.
Alice. 25 anos. 173cm.
Denise. 23 anos. 180cm.
```
