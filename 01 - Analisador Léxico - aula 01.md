# Adaptado de Construção de Compiladores - Daniel Lucrédio, Helena Caseli, Mário César San Felice e Murilo Naldi (UFSCar)
## Tópico 02 - Análise Léxica - Exemplos

### Demonstração 1 - Analisador léxico “na mão” - parte 1

Primeira tentativa de fazer análise léxica: lendo tokens de 1 caractere (ou no máximo 2)

---
1. Abrir o NetBeans, e criar novo projeto Java (com Ant) “AlgumaLex”

2. Criar um novo arquivo, com extensão .alguma, na raiz do projeto, com um programa de exemplo (salvar com codificação ocidental)

```
:DECLARACOES
argumento:INT
fatorial:INT

:ALGORITMO
% Calcula o fatorial de um número inteiro
LER argumento
ATRIBUIR argumento A fatorial
SE argumento = 0 ENTAO ATRIBUIR 1 A fatorial
ENQUANTO argumento > 1
   INICIO
      ATRIBUIR fatorial * (argumento - 1) A fatorial
      ATRIBUIR argumento - 1 A argumento
   FIM
IMPRIMIR fatorial
```

3. Criar um enum algumalex.TipoToken

```java
package algumalex;
public enum TipoToken {
    PCDeclaracoes, PCAlgoritmo, PCInteiro, PCReal, PCAtribuir, PCA, PCLer,
    PCImprimir, PCSe, PCEntao, PCEnquanto, PCInicio, PCFim,
    OpAritMult, OpAritDiv, OpAritSoma, OpAritSub,
    OpRelMenor, OpRelMenorIgual, OpRelMaiorIgual,
    OpRelMaior, OpRelIgual, OpRelDif,
    OpBoolE, OpBoolOu,
    Delim, AbrePar, FechaPar, Var, NumInt, NumReal, Cadeia, Fim
}
```

4. Criar a classe algumalex.Token

```java
package algumalex;
public class Token {
    public TipoToken nome;
    public String lexema;

    public Token(TipoToken nome, String lexema) {
        this.nome = nome;
        this.lexema = lexema;
    }
    @Override
    public String toString() {
        return "<"+nome+","+lexema+">";
    }
}
```

5. Criar a classe algumalex.LeitorDeArquivosTexto

```java
package algumalex;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
public class LeitorDeArquivosTexto {
    InputStream is;
    public LeitorDeArquivosTexto(String arquivo) {
        try {
            is = new FileInputStream(new File(arquivo));
        } catch (Exception ex) {
            ex.printStackTrace(System.err);
        }
    }
    public int lerProximoCaractere() {
        try {
            int ret = is.read();
            System.out.print((char)ret);
            return ret;
        } catch (Exception ex) {
            ex.printStackTrace(System.err);
            return -1;
        }
    }
}
```

6. Criar a classe algumalex.AlgumaLexico

```java
package algumalex;
public class AlgumaLexico {
    LeitorDeArquivosTexto ldat;
    public AlgumaLexico(String arquivo) {
        ldat = new LeitorDeArquivosTexto(arquivo);
    }
    public Token proximoToken() {
        int caractereLido = -1;
        while((caractereLido = ldat.lerProximoCaractere()) != -1) {
            char c = (char)caractereLido;
            if(c == ' ' || c == '\n') continue;
        }
        return null;
    }
}
```

7. Criar a classe algumalex.Main, com o seguinte código no main() e executar

```java
        AlgumaLexico lex = new AlgumaLexico(COLOQUE_O_CAMINHO_DO_ARQUIVO_ALGUMA_AQUI);
        Token t = null;
        while((t = lex.proximoToken()) != null) {
            System.out.print(t);
        }
```

- FIM DA PARTE 1
- EXECUTE O PROJETO E MOSTRE PARA O PROFESSOR (0,50 pt, se correto e sem erros)

- CONTINUAÇÃO:

8. Adicionar na classe AlgumaLexico [após o comando if(c == ' ' || c == '\n') continue;] o código para os tokens com um único caractere e executar

```java
            if(c == ':') {
                return new Token(TipoToken.Delim,":");
            }
            else if(c == '*') {
                return new Token(TipoToken.OpAritMult,"*");
            }
            else if(c == '/') {
                return new Token(TipoToken.OpAritDiv,"/");
            }
            else if(c == '+') {
                return new Token(TipoToken.OpAritSoma,"+");
            }
            else if(c == '-') {
                return new Token(TipoToken.OpAritSub,"-");
            }
            else if(c == '(') {
                return new Token(TipoToken.AbrePar,"(");
            }
            else if(c == ')') {
                return new Token(TipoToken.FechaPar,")");
            }
```

9. Problema: e tokens com mais de um caractere? Adicionar o seguinte código e explicar

```java
            else if(c == '<') {
                c = (char)ldat.lerProximoCaractere();
                if(c == '>')
                    return new Token(TipoToken.OpRelDif,"<>");
                else if(c == '=')
                    return new Token(TipoToken.OpRelMenorIgual,"<=");
                else return new Token(TipoToken.OpRelMenor,"<");
            }
```

10. Mudar, no arquivo de entrada, a linha do ENQUANTO, e mostrar que ainda está funcionando

```
ENQUANTO argumento <= 1
```

11. Mudar, no arquivo de entrada, a linha do ENQUANTO, e mostrar que agora não está mais funcionando (cuidado para não deixar nenhum espaço depois do símbolo “<”)

```
ENQUANTO argumento <1
```

- FIM DA PARTE 2
- EXECUTE O PROJETO E MOSTRE PARA O PROFESSOR (0,50 pt, se correto e sem erros)

- ENVIE O PROJETO PARA UM REPOSITÓRIO DO GITHUB, POIS IREMOS CONTINUAR TRABALHANDO COM ELE NAS PRÓXIMAS AULAS!

