# Linguagens Formais e Autômatos - Daniel Lucrédio
## Aula 07 - Implementação de Autômatos Finitos

### Demonstração 1 - Implementando um DFA simples

Autômato que reconhece cadeias com número ímpar de 1’s

---

```java
    public static void main(String[] args) {
        String entrada = "01101";
        int posicao = 0;
        int estado = 0;
        while (posicao < entrada.length()) {
            imprimeCI(entrada, estado, posicao);
            char elemento = entrada.charAt(posicao);
            if (estado == 0 && elemento == '0') {
                estado = 0;
            } else if (estado == 0 && elemento == '1') {
                estado = 1;
            } else if (estado == 1 && elemento == '0') {
                estado = 1;
            } else if (estado == 1 && elemento == '1') {
                estado = 0;
            }
            posicao++;
        }
        imprimeCI(entrada, estado, posicao);
        if (estado == 1) {
            System.out.println("Aceita");
        }
        if (estado == 0) {
            System.out.println("Rejeita");
        }
    }

    private static void imprimeCI(String entrada, int estado, int posicao) {
        System.out.print(entrada.substring(0, posicao));
        System.out.print("[q" + estado + "]");
        System.out.println(entrada.substring(posicao));
    }
```
 


### Demonstração 2 - Implementando um DFA genérico

Autômato que reconhece cadeias com número ímpar de 1’s

---

```java
    static int[] aceitacao = {1};
    
    // estados = 0,1,2,...
    // alfabeto = 0,1
    static int[][] transicao = {{0,1},{1,0}};

    static int estadoInicial = 0;

    public static void main(String args[]) {
        int estado = estadoInicial;
        String entrada = "101000";

        int posicao = 0;
        while(posicao < entrada.length()) {
            imprimeCI(entrada, estado, posicao);
            int elemento = Integer.parseInt(entrada.substring(posicao,posicao+1));
            estado = transicao[estado][elemento];
            posicao ++;
        }
        imprimeCI(entrada, estado, posicao);

        boolean aceita = false;
        for(int i:aceitacao) {
            if(estado == i) aceita = true;
        }

        System.out.println("Aceita: "+aceita);
    }
```


### Demonstração 3 - Implementando um NFA genérico com backtracking recursivo

Autômato que reconhece cadeias que terminam em 01

---

```java
public class NFABacktracking {

    static int[] aceitacao = {2};
    static int estadoInicial = 0;
    // estados = 0,1,2,...
    // alfabeto = 0,1
    static int[][][] transicao = {{{0, 1}, {0}}, {{}, {2}}, {{}, {}}};

    public static void main(String args[]) {
        String entrada = "1111101";
        int[] estados = {estadoInicial};
        int[] estadosFinais = testa(entrada, estados, 0);
        if (aceita(estadosFinais)) {
            System.out.println("Aceita");
        } else {
            System.out.println("Rejeita");
        }
    }

    private static int[] testa(String entrada, int[] estados, int posicao) {
        if (posicao == entrada.length()) {
            if(aceita(estados)) {
                return estados;
            }
            System.out.println("<<backtrack>> Fim da cadeia");
            return null;
        }
        int elemento = Integer.parseInt(entrada.substring(posicao, posicao + 1));
        for (int i : estados) {
            imprimeCI(entrada, i, posicao);
            int[] novosEstados = transicao[i][elemento];
            if (novosEstados.length == 0) {
                System.out.println("<<backtrack>> Sem opções");
                return null;
            }
            int[] transicoes = testa(entrada, novosEstados, posicao+1);
            if(transicoes != null) return transicoes;
        }
        return null;
    }

    private static boolean aceita(int[] estados) {
        if(estados == null) return false;
        for (int i : estados) {
            for (int j : aceitacao) {
                if (i == j) {
                    return true;
                }
            }
        }
        return false;
    }

    private static void imprimeCI(String entrada, int estado, int posicao) {
        System.out.print(entrada.substring(0, posicao));
        System.out.print("[q"+estado+"]");
        System.out.println(entrada.substring(posicao));
    }
}
```
 


### Demonstração 4 - Implementando um NFA genérico em paralelo

Autômato que reconhece cadeias que terminam em 01

---

```java
public class NFAParalelo {
    static int[] aceitacao = {2};
    static int estadoInicial = 0;
    // estados = 0,1,2,...
    // alfabeto = 0,1
    static int[][][] transicao = {{{0, 1}, {0}}, {{}, {2}}, {{}, {}}};

    public static void main(String args[]) {
        String entrada = "101101";
        int posicao = 0;
        int[] estados = {estadoInicial};
        while(posicao < entrada.length()) {
            imprimeCI(entrada, estados, posicao);
            int elemento = Integer.parseInt(entrada.substring(posicao,posicao+1));
            int[] novosEstados = new int[]{};
            for(int i:estados) {
                int[] destinoTransicao = transicao[i][elemento];
                novosEstados = uniao(novosEstados, destinoTransicao);
            }
            estados = novosEstados;
            if(estados.length == 0)
                break;
            posicao ++;
        }
        imprimeCI(entrada, estados, posicao);
        if (aceita(estados)) {
            System.out.println("Aceita");
        } else {
            System.out.println("Rejeita");
        }
    }

    private static int[] uniao(int [] estados, int[] novosEstados) {
        Set<Integer> uniao = new TreeSet<Integer>();
        for(int i:estados) uniao.add(i);
        for(int i:novosEstados) uniao.add(i);
        int[] ret = new int[uniao.size()];
        int j = 0;
        for(int i:uniao) ret[j++] = i;
        return ret;
    }

    private static boolean aceita(int[] estados) {
        for (int i : estados) {
            for (int j : aceitacao) {
                if (i == j) {
                    return true;
                }
            }
        }
        return false;
    }

    private static void imprimeCI(String entrada, int[] estados, int posicao) {
        System.out.print(entrada.substring(0, posicao)+"{");
        for(int i = 0;i<estados.length; i++) {
            System.out.print("q"+estados[i]);
            if(i < estados.length-1)
                System.out.print(",");
        }
        System.out.println("}"+entrada.substring(posicao));
    }
}
```


### Demonstração 5 - Implementando um ε-NFA genérico em paralelo

Autômato que reconhece cadeias onde 1 e 2 são sempre precedidos por um 0

---

```java
public class ENFA {

    static int[] aceitacao = {0};
    static int estadoInicial = 0;
    // estados = 0,1,2,...
    // alfabeto = 0,1,2
    static int[][][] transicao = {{{1}, {}, {}}, {{}, {2}, {2}}, {{}, {}, {}}};
    static int[][] transicaoVazia = {{}, {0}, {0}};

    public static void main(String args[]) {
        String entrada = "10201";
        int posicao = 0;
        int[] estados = eclose(new int[]{estadoInicial});
        while (posicao < entrada.length()) {
            imprimeCI(entrada, estados, posicao);
            int elemento = Integer.parseInt(entrada.substring(posicao, posicao + 1));
            int[] novosEstados = new int[]{};
            for (int i : estados) {
                int[] destinoTransicao = transicao[i][elemento];
                novosEstados = uniao(novosEstados, destinoTransicao);
                novosEstados = eclose(novosEstados);
            }
            estados = novosEstados;
            if (estados.length == 0) {
                break;
            }
            posicao++;
        }
        imprimeCI(entrada, estados, posicao);
        if (aceita(estados)) {
            System.out.println("Aceita");
        } else {
            System.out.println("Rejeita");
        }
    }

    private static int[] eclose(int[] estados) {
        int[] eclose = estados;
        for (int i : estados) {
            int[] ecloseAux = transicaoVazia[i];
            int[] ecloseAux2 = eclose(ecloseAux);
            eclose = uniao(eclose, ecloseAux);
            eclose = uniao(eclose, ecloseAux2);
        }
        return eclose;
    }

    private static int[] uniao(int[] estados, int[] novosEstados) {
        Set<Integer> uniao = new TreeSet<Integer>();
        for (int i : estados) {
            uniao.add(i);
        }
        for (int i : novosEstados) {
            uniao.add(i);
        }
        int[] ret = new int[uniao.size()];
        int j = 0;
        for (int i : uniao) {
            ret[j++] = i;
        }
        return ret;
    }

    private static boolean aceita(int[] estados) {
        for (int i : estados) {
            for (int j : aceitacao) {
                if (i == j) {
                    return true;
                }
            }
        }
        return false;
    }

    private static void imprimeCI(String entrada, int[] estados, int posicao) {
        System.out.print(entrada.substring(0, posicao) + "{");
        for (int i = 0; i < estados.length; i++) {
            System.out.print("q" + estados[i]);
            if (i < estados.length - 1) {
                System.out.print(",");
            }
        }
        System.out.println("}" + entrada.substring(posicao));
    }
}
```


### Demonstração 6 - Implementando um ε-NFA genérico

Autômato que reconhece números decimais

---

```java
public class StringUtils {

    /**
     * Converte uma string no formato de conjunto, separado por vírgulas em um
     * array de inteiros. Exemplo: {1,6,7}
     */
    public static int[] stringToIntArray(String str) throws RuntimeException {
        if (!str.startsWith("{") || !str.endsWith("}")) {
            throw new RuntimeException("Erro ao converter string para array de "
                    + "inteiros");
        }
        str = str.substring(1, str.length() - 1);
        StringTokenizer st = new StringTokenizer(str, ",");
        int numInteiros = st.countTokens();
        int[] ret = new int[numInteiros];
        int i = 0;
        while (st.hasMoreTokens()) {
            String token = st.nextToken();
            ret[i++] = Integer.parseInt(token);
        }
        return ret;
    }
    
    public static String intArrayToString(int[] array) {
        String ret = "{";
        for(int i:array) {
            ret += i+" ";
        }
        
        return ret+"}";
    }
}
```

```java
import java.io.BufferedReader;
import java.util.Set;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class ENFA {

    private boolean debug = false;
    private int[] aceitacao;
    private int estadoInicial;
    private int[][][] transicao;
    private int[][] transicaoVazia;
    private String mapeamentoEntrada;

    public ENFA(BufferedReader configuracao, boolean debug) {
        this(configuracao);
        this.debug = debug;
    }

    public ENFA(BufferedReader configuracao) {
        try {
            // Primeira linha define o estado inicial
            String linha = configuracao.readLine();
            int i = linha.indexOf(':') + 1;
            String strEstadoInicial = linha.substring(i).trim();
            estadoInicial = Integer.parseInt(strEstadoInicial);

            // Segunda linha define os estados de aceitação
            linha = configuracao.readLine();
            i = linha.indexOf(':') + 1;
            String strEstadosAceitacao = linha.substring(i).trim();
            aceitacao = StringUtils.stringToIntArray(strEstadosAceitacao);

            // Terceira linha define o número de estados
            linha = configuracao.readLine();
            i = linha.indexOf(':') + 1;
            String strNumEstados = linha.substring(i).trim();
            int numEstados = Integer.parseInt(strNumEstados);

            // Quarta linha demarca o inicio da tabela de transicoes
            configuracao.readLine();

            // Quinta linha define os símbolos do alfabeto
            linha = configuracao.readLine();
            StringTokenizer st = new StringTokenizer(linha);
            int numSimbolosAlfabeto = st.countTokens() - 1;
            int numSimbolosAlfabetoExpandido = 0;
            int[] expandir = new int[numSimbolosAlfabeto];
            i = 0;
            mapeamentoEntrada = "";
            while (st.hasMoreTokens()) {
                String token = st.nextToken().trim();
                if (!token.equals("{}")) {
                    if (token.length() == 1) {
                        mapeamentoEntrada += token;
                        expandir[i++] = 1;
                        numSimbolosAlfabetoExpandido++;
                    } else {
                        if (token.length() != 3) {
                            throw new RuntimeException("Erro na definição do "
                                    + "conjunto de símbolos: " + token);
                        }
                        char inicio = token.charAt(0);
                        char fim = token.charAt(2);
                        String expandido = "";
                        for (int c = inicio; c <= fim; c++) {
                            expandido += (char) c;
                        }
                        mapeamentoEntrada += expandido;
                        expandir[i++] = expandido.length();
                        numSimbolosAlfabetoExpandido += expandido.length();
                    }
                }
            }

            // As próximas linhas, até o "FIM", definem as transições
            transicao = new int[numEstados][numSimbolosAlfabetoExpandido][];
            transicaoVazia = new int[numEstados][];

            while (!(linha = configuracao.readLine()).equals("FIM")) {
                st = new StringTokenizer(linha);
                int estado = Integer.parseInt(st.nextToken().trim());
                int indiceSimboloAlfabeto = 0;
                i = 0;
                while (st.hasMoreTokens()) {
                    String strTransicao = st.nextToken().trim();
                    int[] destinoTransicao = StringUtils.stringToIntArray(strTransicao);
                    if (st.hasMoreElements()) {

                        for (int j = 0; j < expandir[indiceSimboloAlfabeto]; j++) {
                            transicao[estado][i++] = destinoTransicao;
                        }
                    } else {
                        transicaoVazia[estado] = destinoTransicao;
                    }
                    indiceSimboloAlfabeto++;
                }
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public boolean executar(String entrada) {
        int posicao = 0;
        int[] estados = eclose(new int[]{estadoInicial});
        while (posicao < entrada.length()) {
            if (debug) {
                imprimeCI(entrada, estados, posicao);
            }
            String elemento = entrada.substring(posicao, posicao + 1);

            int[] novosEstados = new int[]{};
            for (int i : estados) {
                int iElemento = mapeamentoEntrada.indexOf(elemento);
                if (iElemento == -1) {
                    return false;
                }
                int[] destinoTransicao = transicao[i][iElemento];

                novosEstados = uniao(novosEstados, destinoTransicao);
                novosEstados = eclose(novosEstados);
            }
            estados = novosEstados;
            if (estados.length == 0) {
                break;
            }
            posicao++;
        }
        if (debug) {
            imprimeCI(entrada, estados, posicao);
        }
        if (aceita(estados)) {
            return true;
        } else {
            return false;
        }
    }

    private int[] eclose(int[] estados) {
        int[] eclose = estados;
        for (int i : estados) {
            int[] ecloseAux = transicaoVazia[i];
            int[] ecloseAux2 = eclose(ecloseAux);
            eclose = uniao(eclose, ecloseAux);
            eclose = uniao(eclose, ecloseAux2);
        }
        return eclose;
    }

    private int[] uniao(int[] estados, int[] novosEstados) {
        Set<Integer> uniao = new TreeSet<Integer>();
        for (int i : estados) {
            uniao.add(i);
        }
        for (int i : novosEstados) {
            uniao.add(i);
        }
        int[] ret = new int[uniao.size()];
        int j = 0;
        for (int i : uniao) {
            ret[j++] = i;
        }
        return ret;
    }

    private boolean aceita(int[] estados) {
        for (int i : estados) {
            for (int j : aceitacao) {
                if (i == j) {
                    return true;
                }
            }
        }
        return false;
    }

    private void imprimeCI(String entrada, int[] estados, int posicao) {
        System.out.print(entrada.substring(0, posicao) + "{");
        for (int i = 0; i < estados.length; i++) {
            System.out.print("q" + estados[i]);
            if (i < estados.length - 1) {
                System.out.print(",");
            }
        }
        System.out.println("}" + entrada.substring(posicao));
    }

    public void imprimirConfiguracaoMaquina() {
        System.out.println("Estado inicial:" + estadoInicial);
        System.out.println("Estados de aceitação:" + StringUtils.intArrayToString(aceitacao));
        System.out.println("Mapeamento de entrada:" + mapeamentoEntrada);
        System.out.println("Transições:");
        int i = 0;
        for (int[][] estados : transicao) {
            System.out.print(i + ":");
            for (int[] transicoes : estados) {
                System.out.print(StringUtils.intArrayToString(transicoes) + " ");
            }
            System.out.println();
            i++;
        }
        System.out.println("Transições vazias:");

        i = 0;
        for (int[] transicoes : transicaoVazia) {
            System.out.print(i + ":");
            System.out.print(StringUtils.intArrayToString(transicoes) + " ");

            System.out.println();
            i++;
        }
    }
}
```

```java
package enfa;

import java.io.BufferedReader;
import java.io.InputStreamReader;

public class Principal {

    public static void main(String[] args) {
        ENFA enfa = new ENFA(new BufferedReader(new InputStreamReader(ENFA.class.getResourceAsStream("maquinaEstados.txt"))));
        //enfa.imprimirConfiguracaoMaquina();

        String[] testes = {"", "+", "+2", "-2", "+-2", "2", "10", "222", "2321", "2.321", "22.341", "1.344.233", "1344.222", "233.12",
            ",12", "0,12", "2,33", "4,3324242", "23,", "0,0", "2,333.444", "133.441.989,951234222"};
        for (String t : testes) {
            System.out.println("\""+t + "\" = " + (enfa.executar(t) ? "Válido" : "Inválido"));
        }
    }
}
```

- maquinaEstados.txt:

```
Estado inicial:0
Estados aceitação:{4,9}
Número de estados:10
INICIO
    +       -       0-9     ,       .       {}
0   {1}     {1}     {}      {}      {}      {1}
1   {}      {}      {2,4}   {}      {}      {}
2   {}      {}      {3,4}   {}      {}      {}
3   {}      {}      {4}     {}      {}      {}
4   {}      {}      {}      {8}     {5}     {}
5   {}      {}      {6}     {}      {}      {}
6   {}      {}      {7}     {}      {}      {}
7   {}      {}      {4}     {}      {}      {}
8   {}      {}      {9}     {}      {}      {}
9   {}      {}      {9}     {}      {}      {}
FIM
```
