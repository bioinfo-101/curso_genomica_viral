# Curso de Genômica Viral - Chikungunya

Este repositório contém o conteúdo prático do minicurso "Aplicações de bioinformática na vigilância genômica de patógenos virais", a parte teórica está disponível [neste vídeo no youtube](https://www.youtube.com/watch?v=8NZM8kYi57o).

Nesta parte prática realizaremos a análise genômica de Chikungunya vírus a partir de dados artificais simulando um sequenciamento Illumina paired-end de reads de 250 bases, abrangendo desde o controle de qualidade dos dados até análises filogenéticas e visualização com Nextstrain/Auspice.

## 📋 Objetivos de Aprendizagem

Ao final deste curso, você será capaz de:

- Realizar controle de qualidade de dados de sequenciamento
- Montar genomas virais por referência usando BWA, SAMtools e iVar
- Analisar genomas com Nextclade para identificação de genótipos
- Coletar e filtrar dados genômicos do NCBI
- Construir análises filogenéticas com Nextstrain/Augur
- Visualizar resultados em plataformas interativas (Auspice)

## 🔧 Pré-requisitos

- Uma conta no github
- Vontade de aprender

## 📚 Estrutura do Curso

### [Módulo 00: Preparação do Ambiente](00_ambiente/)
- Instalação do Micromamba e todas as dependências necessárias.

### [Módulo 01: Controle de Qualidade](01_qc/)
- Análise de qualidade com fastp

### [Módulo 02: Mapeamento e Montagem](02_mapping/)
- Indexação de referência com BWA
- Mapeamento de reads contra referência
- Processamento com SAMtools
- Geração de consenso com iVar

### [Módulo 03: Análise com Nextclade](03_nextclade/)
- Análise de QC do genoma consenso
- Identificação de genótipos
- Detecção de mutações
- Dataset: [chikV/genotypes](https://github.com/nextstrain/nextclade_data/tree/master/data/community/v-gen-lab/chikV/genotypes)

### [Módulo 04: Coleta de Dados NCBI](04_ncbi_data/)
- Download de genomas via NCBI Datasets (taxid: 37124)
- Preparação de metadados
- Filtragem e sampling com Augur Filter

### [Módulo 05: Análise Filogenética](05_filogenia/)
- Alinhamento com MAFFT (via augur align)
- Mascaramento de regiões UTR
- Construção de árvore filogenética
- Inferência de traits geográficos
- Anotação de mutações e clados

## 📖 Navegação

Cada módulo possui seu próprio `README.md` com instruções detalhadas. Recomenda-se seguir os módulos em ordem sequencial.

## 📂 Estrutura de Outputs

Os outputs gerados durante o curso são organizados em subdiretórios específicos para cada módulo, mantendo uma estrutura limpa e organizada:

```
outputs/
├── 01_qc/              # Reads filtrados e relatórios do fastp
├── 02_mapping/         # Arquivos SAM/BAM e consensos FASTA
├── 03_nextclade/       # Resultados de genotipagem e QC
├── 04_ncbi_data/       # Genomas do NCBI e dataset combinado
└── 05_filogenia/       # Árvores filogenéticas e arquivos Auspice
```

### Fluxo de Dados

Os outputs de cada módulo servem como inputs para os módulos subsequentes:

1. **Módulo 01 (QC)** → gera reads limpos em `outputs/01_qc/`
2. **Módulo 02 (Mapping)** → lê de `01_qc/`, gera consensos em `outputs/02_mapping/`
3. **Módulo 03 (Nextclade)** → lê consensos de `02_mapping/`, gera análises em `outputs/03_nextclade/`
4. **Módulo 04 (NCBI)** → combina consensos de `02_mapping/` com dados NCBI em `outputs/04_ncbi_data/`
5. **Módulo 05 (Filogenia)** → lê dataset combinado de `04_ncbi_data/`, gera árvores em `outputs/05_filogenia/`


## 🔍 Ferramentas Utilizadas

| Ferramenta | Versão | Uso |
|------------|--------|-----|
| fastp | 0.23.4 | Controle de qualidade |
| BWA | 0.7.18 | Mapeamento de reads |
| SAMtools | 1.21 | Manipulação de SAM/BAM |
| iVar | 1.4.3 | Geração de consenso |
| Nextclade | 3.15 | Genotipagem viral e controle de qualidade |
| NCBI Datasets | 18.9 | Download de genomas |
| Augur | 32.0 | Análises filogenéticas |
| IQ-TREE | 3.0 | Construção de árvores |
| MAFFT | 7.5 | Alinhamento múltiplo |

## 📚 Referências

Para citações e materiais de referência, consulte [references.md](docs/references.md).

## 📧 Contato

Para dúvidas ou sugestões, entre em contato via email: zimmer.filipe@gmail.com

## 📄 Licença

Este material é disponibilizado para fins educacionais, qualquer uso comercial deve ser feito com permissão do autor.
