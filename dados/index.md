# Sobre a manipulação dos dados

```bash
wget -c http://agencia.tse.jus.br/estatistica/sead/odsele/consulta_coligacao/consulta_coligacao_2018.zip
```

```bash
unzip consulta_coligacao_2018.zip
```

```bash
for arquivo in consulta_coligacao_2018_??.csv; do
	cut -f14,21 -d\; "$arquivo" | sort | uniq | egrep --text -v '(SUPLENTE|DS_.*|VICE)' > "${arquivo#consulta_coligacao_2018_}"
done
```

```bash
for arquivo in ??.csv; do
	sed -i '1i"Cargo";"Coligação"' "$arquivo"
done
```
