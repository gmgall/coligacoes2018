# Sobre a manipulação dos dados

Os dados das coligações foram baixados do [Repositório de Dados
Eleitorais](http://www.tse.jus.br/eleicoes/estatisticas/repositorio-de-dados-eleitorais-1/repositorio-de-dados-eleitorais) do site do TSE no dia 20/09/2018. 

```bash
wget -c http://agencia.tse.jus.br/estatistica/sead/odsele/consulta_coligacao/consulta_coligacao_2018.zip
```

Os dados são fornecidos num [ZIP](https://pt.wikipedia.org/wiki/ZIP) que contém um [CSV](https://pt.wikipedia.org/wiki/Comma-separated_values)
para os dados de coligação de cada UF.

```bash
unzip consulta_coligacao_2018.zip
```

Os CSVs possuem 21 colunas. O caractere separador é `;`. Uma descrição do que significa cada coluna está no
arquivo `leiame.pdf` presente no ZIP baixado. Meu interesse está nas 14ª e 21ª
colunas. Elas contêm o cargo e os partidos que estão na coligação, respectivamente.
Seleciono apenas essas colunas com o comamando `cut`.

Cada combinação de cargo + partido aparece uma vez. A coligação *O Rio quer paz*
do RJ, por exemplo, aparece quatro vezes porque é composta por 4 partidos. Essa
redundância é eliminada com `sort | uniq`.

Por fim, excluo as linhas que contém as palavras `SUPLENTE` (porque são iguais
as de senador) e `VICE` (porque são iguais as de governador). Excluo também a
linha com os nomes das colunas. Isso é feito com a opção `-v '(SUPLENTE|DS_.*|VICE)`
do comando `egrep`. Uso a opção `--text` para evitar a mensagem `Arquivo binário coincide com o padrão`.
A codificação dos arquivos é latin1, meu sistema usa UTF-8.

Cada arquivo processado é salvo num novo arquivo com nome `UF.csv`.

```bash
for arquivo in consulta_coligacao_2018_??.csv; do
	cut -f14,21 -d\; "$arquivo" | sort | uniq | egrep --text -v '(SUPLENTE|DS_.*|VICE)' > "${arquivo#consulta_coligacao_2018_}"
done
```

Adicionei uma linha com o nome das colunas nos arquivos processados com o
comando `sed`.

```bash
for arquivo in ??.csv; do
	sed -i '1i"Cargo";"Coligação"' "$arquivo"
done
```

Importei manualmante cada arquivo CSV resultante como uma folha de planilha do
Google Sheets disponível
[aqui](https://docs.google.com/spreadsheets/d/1g7afzfqL3mvhe9bqqo6y5a8CEif8_KNJIfgxl7ct3NQ/edit?usp=sharing).

As versões dos programas usados foram:

```bash
$ wget -V
GNU Wget 1.17.1 construído em linux-gnu.

$ unzip -v
UnZip 6.00 of 20 April 2009, by Debian. Original by Info-ZIP.

Compiled with gcc 5.2.1 20151119 for Unix (Linux ELF).

+digest -gpgme +https +ipv6 +iri +large-file -metalink +nls +ntlm 
+opie -psl +ssl/openssl 

$ cut --version
cut (GNU coreutils) 8.25

$ sort --version
sort (GNU coreutils) 8.25

$ uniq --version
uniq (GNU coreutils) 8.25

$ egrep --version
grep (GNU grep) 2.25
```
