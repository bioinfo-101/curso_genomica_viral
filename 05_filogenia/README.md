# Módulo 05: Análise Filogenética com Nextstrain/Augur

## Objetivos

- Alinhar sequências com Augur
- Mascarar regiões UTR
- Construir árvore filogenética
- Inferir traits geográficos
- Anotar mutações
- Exportar para visualização no Auspice

## Ferramentas

- **Augur**: Suite de ferramentas Nextstrain para análises filogenéticas
- **MAFFT**: Alinhamento múltiplo (usado internamente pelo Augur)
- **IQ-TREE**: Construção de árvores filogenéticas (usado internamente pelo Augur)

## Fluxo de Trabalho

```
Sequências → Align → Mask → Tree → Refine → Ancestral → Translate → Traits → Export
```

## Passo 1: Ativar o Ambiente

```bash
micromamba activate curso
cd /workspaces/curso_genomica_viral/05_filogenia
```

## Passo 2: Executar Workflow com Snakemake

Como aqui teremos uma série de passos, usaremos um workflow em linguagem Snakemake para rodar todas as nossas análises. Os arquivos de input estão definidos como variáveis no código (hardcoded), portanto devemos primeiro rodar todas as etapas anteriores (módulos 01-04) para gerar os arquivos necessários.

### Arquivos de Entrada (definidos no Snakefile)

- `../outputs/04_ncbi_data/combined_dataset.fasta` - Dataset combinado (gerado no Módulo 04)
- `../outputs/04_ncbi_data/combined_metadata.tsv` - Metadados combinados (gerado no Módulo 04)
- `../inputs/reference.gb` - Referência em formato GenBank
- `../inputs/mask.bed` - Arquivo BED com regiões a mascarar

### Executar o Workflow

```bash
# Executar Snakemake com 2 núcleos
snakemake --cores 2 --printshellcmds
```

## Regras do Workflow

O Snakefile executa as seguintes etapas automaticamente:

### 1. `align` - Alinhamento

Alinha todas as sequências contra a referência usando MAFFT (via Augur).

**Entrada**: `../outputs/04_ncbi_data/combined_dataset.fasta`, `../inputs/reference.gb`  
**Saída**: `../outputs/05_filogenia/aligned.fasta`

### 2. `mask` - Mascaramento

Mascara regiões UTR definidas no arquivo `../inputs/mask.bed`.

**Entrada**: Alinhamento, arquivo BED com coordenadas  
**Saída**: `../outputs/05_filogenia/masked.fasta`

### 3. `tree` - Construção da Árvore

Constrói árvore filogenética com IQ-TREE usando:
- Modelo GTR+F
- Seed 123 para reproducibilidade
- 1000 réplicas de bootstrap (aproximado com -alrt)
- 2 threads

**Entrada**: Alinhamento mascarado  
**Saída**: `../outputs/05_filogenia/tree_raw.nwk`

### 4. `refine` - Refinamento

Refina árvore com estimativa de datas usando TreeTime:
- Modelo coalescente otimizado (`opt`)
- Inferência conjunta de datas (`joint`)
- Intervalos de confiança para datas

**Entrada**: Árvore bruta, alinhamento, metadados  
**Saída**: `../outputs/05_filogenia/tree.nwk`, `../outputs/05_filogenia/branch_lengths.json`

### 5. `ancestral` - Sequências Ancestrais

Reconstrói sequências ancestrais nos nós internos usando inferência conjunta (joint).

**Entrada**: Árvore refinada, alinhamento mascarado  
**Saída**: `../outputs/05_filogenia/nt_muts.json`

### 6. `translate` - Tradução

Traduz mutações nucleotídicas para aminoácidos com base nas anotações do arquivo GenBank.

**Entrada**: Árvore, mutações nucleotídicas, referência  
**Saída**: `../outputs/05_filogenia/aa_muts.json`

### 7. `traits` - Inferência de Traits

Infere localização geográfica dos nós ancestrais usando o campo `location` dos metadados.

**Entrada**: Árvore, metadados  
**Saída**: `../outputs/05_filogenia/traits.json`

### 8. `export` - Exportar para Auspice

Exporta todos os dados para formato JSON do Auspice, incluindo:
- Configurações de cores (`config/colors.tsv`)
- Coordenadas geográficas (`config/lat_longs.tsv`)
- Configurações do Auspice (`config/auspice_config.json`)

**Saída**: `../outputs/05_filogenia/chikv.auspice.json`

## Estrutura de Saída

Todos os arquivos de saída são gerados em `../outputs/05_filogenia/`:

```
../outputs/05_filogenia/
├── aligned.fasta          # Alinhamento
├── masked.fasta           # Alinhamento mascarado
├── tree_raw.nwk           # Árvore bruta
├── tree.nwk               # Árvore refinada com datas
├── branch_lengths.json    # Comprimentos de ramo e datas
├── nt_muts.json           # Mutações nucleotídicas
├── aa_muts.json           # Mutações aminoácidas
├── traits.json            # Localização geográfica inferida
└── chikv.auspice.json     # JSON final para visualização
```

## Interpretação dos Resultados

### Árvore Filogenética

- **Newick (.nwk)**: Formato de texto da árvore
- `tree_raw.nwk`: Árvore filogenética sem datação
- `tree.nwk`: Árvore com estimativa de datas (timetree)

Pode ser visualizada em:
  - [iTOL](https://itol.embl.de/)
  - [FigTree](http://tree.bio.ed.ac.uk/software/figtree/)

### Arquivos JSON

Contêm anotações da árvore:
- **branch_lengths.json**: Datas inferidas dos nós e comprimentos de ramo
- **nt_muts.json**: Mutações nucleotídicas em cada ramo
- **aa_muts.json**: Mutações de aminoácidos por gene
- **traits.json**: Localização geográfica inferida dos ancestrais

### Arquivo Auspice

O arquivo `chikv.auspice.json` contém todos os dados integrados para visualização interativa no Auspice, aqui usaremos a [plataforma web](https://auspice.us/) mas também é possível rodar o auspice via linha de comando.

## Próximos Passos

Agora está na hora de você praticar sozinho(a)!

## Recursos Adicionais

- [Augur Documentation](https://docs.nextstrain.org/projects/augur/)
- [Nextstrain Tutorial](https://nextstrain.org/docs/tutorials/)
- [IQ-TREE Manual](http://www.iqtree.org/doc/)
- [Snakemake Documentation](https://snakemake.readthedocs.io/)
