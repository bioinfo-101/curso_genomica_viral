# Módulo 04: Coleta de Dados do NCBI

## Objetivos

- Baixar genomas de Chikungunya do NCBI via `ncbi-datasets`
- Filtrar e amostrar sequências com `augur filter`
- Preparar dataset para análise filogenética

## Ferramentas

- **ncbi-datasets-cli**: Download de dados do NCBI
- **augur**: Filtragem e sampling de sequências com o comando filter

## Informações do Vírus

- **Vírus**: Chikungunya virus
- **Taxon ID**: 37124
- **Família**: Togaviridae
- **Gênero**: Alphavirus

## Fluxo de Trabalho

```
NCBI (taxid 37124) → ncbi-datasets → Descompactar → augur filter → Dataset filtrado
```

## Passo 1: Ativar o Ambiente

```bash
micromamba activate curso
cd /workspaces/curso_genomica_viral/04_ncbi_data
```

## Passo 2: Baixar Genomas do NCBI

### Parâmetros que utilizaremos

#### datasets

- `taxon 37124`: ID taxonômico do Chikungunya
- `--complete-only`: Apenas genomas completos

#### dataformat

- `--inputfile`: Arquivo de entrada
- `--fields`: Campos a serem extraídos

Aqui extraímos apenas os campos que serão úteis para o augur filter: nome da sequência, data de coleta e localização geográfica. Além disso, o augur precisa que o nome das colunas seja `strain`, `date` e `location`, portanto, usamos o `sed` para renomear as colunas.

#### sed

Aqui usamos o `sed` para renomear as colunas e remover as colunas que não serão úteis para o augur filter e também fazemos uma limpeza em que removemos o caractere `:` seguido de qualquer coisa (é um regex), na lógica deste nosso arquivo, isso acontece apenas na terceira coluna que contém a localização geográfica, onde o ncbi traz alguns nomes de subregiões (por exemplo `Brazil: Alagoas, Maceio`) que não serão úteis para a nossa estratégia de sampling neste curso.

```bash
# Criar diretório de outputs
mkdir -p ../outputs/04_ncbi_data

# Baixar genomas completos de Chikungunya
datasets download virus genome taxon 37124 \
    --complete-only \
    --filename chikv_genomes.zip

# Descompactar
unzip chikv_genomes.zip -d chikv_genomes

# Verifique as sequências
more chikv_genomes/ncbi_dataset/data/genomic.fna

# Copie para o diretório de outputs formatando o cabeçalho para conter apenas o id de acesso de cada sequência 
sed 's/ .*//g' chikv_genomes/ncbi_dataset/data/genomic.fna > ../outputs/04_ncbi_data/ncbi_chikv_genomes.fasta

# Verifique as sequências com os novos nomes
more ../outputs/04_ncbi_data/ncbi_chikv_genomes.fasta
# ou
grep ">" ../outputs/04_ncbi_data/ncbi_chikv_genomes.fasta

# Crie um arquivo tabular de metadados que serão úteis para o augur filter
dataformat tsv virus-genome \
    --inputfile chikv_genomes/ncbi_dataset/data/data_report.jsonl \
    --fields accession,isolate-collection-date,geo-location |
    sed -e "s/Accession/strain/g" \
        -e "s/Isolate Collection date/date/g" \
        -e "s/Geographic Location/location/g" \
        -e "s/\:.*//g" > ../outputs/04_ncbi_data/ncbi_chikv_metadata.tsv

# Verifique os metadados
more ../outputs/04_ncbi_data/ncbi_chikv_metadata.tsv
```

## Passo 3: Filtrar e Amostrar Sequências

### Parâmetros que utilizaremos

- `--sequences`: Arquivo FASTA de entrada
- `--metadata`: Metadados em formato TSV
- `--output-sequences`: Arquivo fasta de saída filtrado
- `--output-metadata`: Arquivo tsv de metadados filtrados
- `--min-length`: Comprimento mínimo (~80% do genoma de referência)
- `--max-length`: Comprimento máximo (~100,05% do genoma de referência)
- `--group-by`: Agrupar por país, ano e mês para amostragem
- `--sequences-per-group`: Máximo de sequências por grupo
- `--subsample-seed`: Seed para reproducibilidade

```bash
augur filter \
    --sequences ../outputs/04_ncbi_data/ncbi_chikv_genomes.fasta \
    --metadata ../outputs/04_ncbi_data/ncbi_chikv_metadata.tsv \
    --output-sequences ../outputs/04_ncbi_data/ncbi_chikv_genomes.filtered.fasta \
    --output-metadata ../outputs/04_ncbi_data/ncbi_chikv_metadata.filtered.tsv \
    --min-length 9460 \
    --max-length 11832 \
    --group-by location year month \
    --sequences-per-group 3 \
    --subsample-seed 123

# conte o número de genomas antes e depois do filtro:
grep -c ">" ../outputs/04_ncbi_data/ncbi_chikv_genomes*fasta
```

## Passo 4: Combinar com Consensos do Curso

```bash
# Combinar seus consensos com dados do NCBI
cat ../outputs/04_ncbi_data/ncbi_chikv_genomes.filtered.fasta ../outputs/02_mapping/Art.fa > ../outputs/04_ncbi_data/combined_dataset.fasta
cat ../outputs/04_ncbi_data/ncbi_chikv_metadata.filtered.tsv ../inputs/metadata.tsv > ../outputs/04_ncbi_data/combined_metadata.tsv
```

## Próximos Passos

Com o dataset preparado, prossiga para:
- [Módulo 05: Análise Filogenética](../05_filogenia/)

## Recursos Adicionais

- [NCBI Datasets CLI](https://www.ncbi.nlm.nih.gov/datasets/docs/v2/reference-docs/command-line/datasets/)
- [Augur Filter](https://docs.nextstrain.org/projects/augur/en/stable/usage/cli/filter.html)
