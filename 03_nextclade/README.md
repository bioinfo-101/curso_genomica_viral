# Módulo 03: Análise com Nextclade

## Objetivos

- Compreender o Nextclade para análise de genomas virais
- Utilizar o dataset de genótipos de Chikungunya
- Interpretar resultados de QC e genotipagem

## Ferramenta

- **Nextclade**: Análise rápida de QC, genotipagem e mutações em genomas virais

## Dataset Utilizado

**chikV/genotypes** - Dataset comunitário de genótipos de Chikungunya
- Repositório: https://github.com/nextstrain/nextclade_data/tree/master/data/community/v-gen-lab/chikV/genotypes
- Mantido por: v-gen-lab
- Características: Clados baseados nos genótipos de Chikungunya:
    - ECSA: East/Central/South African
    - Asian: Asiático
    - West African: África Ocidental

## Fluxo de Trabalho

```
Consensos FASTA → Nextclade run → Resultados (TSV, JSON, alignment, QC)
```

## Passo 1: Ativar o Ambiente

```bash
micromamba activate curso
cd /workspaces/curso_genomica_viral/03_nextclade
```

## Passo 2: Executar Nextclade

Utilizaremos o arquivo de consensos que foi gerado no módulo 02.

Você pode checar os datasets existentes com:

```bash
nextclade dataset list
```

E fazer o download de uma cópia local do dataset com:

```bash
nextclade dataset get \
    --name community/v-gen-lab/chikV/genotypes \
    --output-dir dataset/
```

Nesta prática, rodaremos o nextclade passando o nome do diretório remoto.

```bash
mkdir -p ../outputs/03_nextclade

nextclade run \
    --dataset-name community/v-gen-lab/chikV/genotypes \
    --output-all ../outputs/03_nextclade/ \
    ../outputs/02_mapping/Art.fa
```


## Estrutura de Saída

```
../outputs/03_nextclade/
├── nextclade.tsv                         # Resultados tabulares principais
├── nextclade.json                        # Resultados em JSON
├── nextclade.aligned.fasta               # Sequências alinhadas
├── nextclade.insertions.csv              # Inserções detectadas
├── nextclade.gff                         # Arquivo gff da sequência de input
├── nextclade.nwk                         # Arquivo nwk da árvore de referência com a nova sequência
├── nextclade.auspice.json                # Arquivo json para visualização no auspice
└── nextclade.cds_translation.<cds>.fasta # Sequencia da região específica traduzida
```

## Interpretação dos Resultados

### Arquivo `nextclade.tsv`

Principais colunas:

| Coluna | Descrição |
|--------|-----------|
| `seqName` | Nome da sequência |
| `clade` | Genótipo/clado identificado |
| `qc.overallStatus` | Status QC global (good/mediocre/bad) |
| `qc.overallScore` | Score QC (0-100, menor é melhor) |
| `totalMissing` | Bases faltantes (N's) |
| `totalSubstitutions` | Número de substituições |
| `totalDeletions` | Bases deletadas |
| `totalInsertions` | Bases inseridas |
| `substitutions` | Lista de mutações |
| `aaSubstitutions` | Mutações de aminoácidos |
| `coverage` | % de cobertura |

### Status de QC

- ✅ **good**: Sequência de boa qualidade
- ⚠️ **mediocre**: Qualidade aceitável, mas com limitações
- ❌ **bad**: Qualidade ruim, usar com cautela

### Critérios de QC

O Nextclade avalia:
- **Missing data**: % de N's
- **Mixed sites**: Bases ambíguas
- **Private mutations**: Mutações únicas
- **Frame shifts**: Mudanças de quadro de leitura
- **Stop codons**: Códons de parada prematuros

## Interpretação de Clados/Genótipos

O dataset utilizado possui os seguintes genótipos:
- **ECSA** (East/Central/South African) "Leste-Centro-Sul Africano"
- **Asian**
- **West African**

O campo `clade` indicará o genótipo identificado.

## Próximos Passos

Após análise inicial com Nextclade:
- Prosseguir para [Módulo 04: Coleta de Dados NCBI](../04_ncbi_data/)

## Recursos Adicionais

- [Documentação Nextclade](https://docs.nextstrain.org/projects/nextclade/)
- [Dataset chikV](https://github.com/nextstrain/nextclade_data/tree/master/data/community/v-gen-lab/chikV)
