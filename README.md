# World-Meter

import java.io.File;
import java.io.FileNotFoundException;
import java.util.ArrayList;
import java.util.Scanner;

public class Main {

    static ArrayList<String> alfas2 =new ArrayList<>();
    static ArrayList<Integer> idsUsados = new ArrayList<>();
    static ArrayList<Cidade> cidades = new ArrayList<>();
    static ArrayList<Populacao> populacao = new ArrayList<>();
    static ArrayList<Pais> paises = new ArrayList<>();
    static int[] inputs1 = new int[3];
    static int[] inputs2 = new int[3];
    static int[] inputs3 = new int[3];

    public static boolean parsePais(File Paises) {

        Scanner scanner = null;

        try {
            scanner = new Scanner(Paises);
        } catch (FileNotFoundException e) {
            return false;
        }

        boolean primeiraLinha = true;
        int linhasIncorretas = 0;
        int linhasCorretas = 0;
        int primeiraLinhaIncorreta = -1;
        int count = 0;

        while (scanner.hasNextLine()) {
            String linha = scanner.nextLine();
            count++;
            if (primeiraLinha) {
                primeiraLinha = false;
            } else {
                String[] coisa = linha.split(",");

                if (coisa.length == 4) {
                    try {
                        int id = Integer.parseInt(coisa[0]);
                        String alfa2 = coisa[1];
                        String alfa3 = coisa[2];
                        String nome = coisa[3];
                        Pais pais = new Pais(id, alfa2, alfa3, nome);

                        if (!idsUsados.contains(id)){
                            idsUsados.add(id);
                            paises.add(pais);
                            alfas2.add(alfa2.toUpperCase());
                            linhasCorretas++;

                        }else{
                            linhasIncorretas++;
                            if (primeiraLinhaIncorreta == -1) {
                                primeiraLinhaIncorreta = count;
                            }
                        }

                    } catch (NumberFormatException e) {
                        linhasIncorretas++;
                        if (primeiraLinhaIncorreta == -1) {
                            primeiraLinhaIncorreta = count;
                        }
                    }

                    }else{
                    linhasIncorretas++;
                    if (primeiraLinhaIncorreta == -1){
                        primeiraLinhaIncorreta = count;
                    }
                }
            }
        }
        inputs1[0] = linhasCorretas;
        inputs1[1] = linhasIncorretas;
        inputs1[2] = primeiraLinhaIncorreta;

        return true;
    }

    public static boolean parseCidade(File Cidades){

        Scanner scanner = null;

        try{
            scanner = new Scanner(Cidades);
        }catch (FileNotFoundException e){
            return false;
        }

        boolean primeiraLinha = true;

        int linhasIncorretas = 0;
        int linhasCorretas = 0;
        int primeiraLinhaIncorreta = -1;

        while (scanner.hasNext()) {
            String linha = scanner.nextLine();

            if (primeiraLinha) {
                primeiraLinha = false;
            } else {
                String[] coisa = linha.split(",");

                if (coisa.length == 6) {
                    try {
                        String alfa2 = coisa[0].toUpperCase();
                        if (alfas2.contains(alfa2)) {
                            String cidadeNome = coisa[1];
                            String regiao = coisa[2];
                            double populacaoDouble = Double.parseDouble(coisa[3]);
                            double latitude = Double.parseDouble(coisa[4]);
                            double longitude = Double.parseDouble(coisa[5]);
                            Cidade cidade = new Cidade(alfa2, cidadeNome, regiao,(int) populacaoDouble, latitude, longitude);
                            cidades.add(cidade);
                            linhasCorretas++;
                        }
                        else {
                            linhasIncorretas++;
                            if (primeiraLinhaIncorreta == -1) {
                                primeiraLinhaIncorreta = linhasCorretas + linhasIncorretas;
                            }
                        }
                    } catch (NumberFormatException e) {
                        linhasIncorretas++;
                        if (primeiraLinhaIncorreta == -1) {
                            primeiraLinhaIncorreta = linhasCorretas + linhasIncorretas;
                        }
                    }
                } else {
                    linhasIncorretas++;
                    if (primeiraLinhaIncorreta == -1) {
                        primeiraLinhaIncorreta = linhasCorretas + linhasIncorretas;
                    }
                }
                inputs2[0] = linhasCorretas;
                inputs2[1] = linhasIncorretas;
                inputs2[2] = primeiraLinhaIncorreta;
            }
        }
        return true;
    }

    public static boolean parsePopulacao(File Populacao){

        Scanner scanner = null;

        try {
            scanner = new Scanner(Populacao);
        }catch (FileNotFoundException e){
            return false;
        }

        boolean primeiraLinha = true;

        int linhasIncorretas = 0;
        int linhasCorretas = 0;
        int primeiraLinhaIncorreta = -1;

        while (scanner.hasNextLine()) {
            String linha = scanner.nextLine();

            if (primeiraLinha) {
                primeiraLinha = false;
            } else {
                String[] partes = linha.split(",");
                if (partes.length == 5) {
                    try {
                        int paisID = Integer.parseInt(partes[0]);
                        if (idsUsados.contains(paisID)) {
                            linhasCorretas++;
                            int id = Integer.parseInt(partes[0]);
                            int ano = Integer.parseInt(partes[1]);
                            int populacaoMasculina = Integer.parseInt(partes[2]);
                            int populacaoFeminina = Integer.parseInt(partes[3]);
                            double densidade = Double.parseDouble(partes[4]);

                            Populacao populacao1 = new Populacao(id, ano, populacaoMasculina, populacaoFeminina, densidade);
                            populacao.add(populacao1);

                            for (Pais pais : paises) {
                                if (pais.id == id && id > 700) {
                                    pais.count++;
                                }
                            }
                        }else {
                                linhasIncorretas++;
                                if (primeiraLinhaIncorreta == -1) {
                                    primeiraLinhaIncorreta = linhasCorretas + linhasIncorretas;
                                }
                              }

                    } catch (NumberFormatException e) {
                        linhasIncorretas++;
                        if (primeiraLinhaIncorreta == -1) {
                            primeiraLinhaIncorreta = linhasCorretas + linhasIncorretas;
                        }
                    }
                }
            }
            inputs3[0] = linhasCorretas;
            inputs3[1] = linhasIncorretas;
            inputs3[2] = primeiraLinhaIncorreta + 1;
        }
        return true;
    }

    public static boolean parseFiles(File folder) {
        cidades.clear();
        paises.clear();
        populacao.clear();
        idsUsados.clear();
        alfas2.clear();

        return parsePais(new File(folder, "paises.csv"))
                && parseCidade(new File(folder, "cidades.csv"))
                && parsePopulacao(new File(folder, "populacao.csv"));
    }

    public static ArrayList getObjects(TipoEntidade tipo) {
        if (tipo == TipoEntidade.PAIS) {
            return paises;
        }
        if (tipo == TipoEntidade.CIDADE) {
            return cidades;
        }
        if (tipo == TipoEntidade.INPUT_INVALIDO) {
            ArrayList<String> inputsInvalidos = new ArrayList<>();
            inputsInvalidos.add("paises.csv | " + inputs1[0] + " | " + inputs1[1] + " | " + inputs1[2]);
            inputsInvalidos.add("cidades.csv | " + inputs2[0] + " | " + inputs2[1] + " | " + inputs2[2]);
            inputsInvalidos.add("populacao.csv | " + inputs3[0] + " | " + inputs3[1] + " | " + inputs3[2]);
            return inputsInvalidos;
        }
        return null;
    }

        public static void main(String[] args) {
        boolean parseOk = parseFiles(new File("testes"));
        if (!parseOk){
            System.out.println("e");
            return;
        }
        System.out.println(getObjects(TipoEntidade.INPUT_INVALIDO));
    }
}
