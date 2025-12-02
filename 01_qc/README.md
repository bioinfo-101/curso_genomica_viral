# Módulo 01: Controle de Qualidade

## Objetivos

- Entender a importância do controle de qualidade em dados de sequenciamento
- Utilizar **fastp** para filtragem e "trimming" de reads

## Ferramentas

- **fastp**: Ferramenta rápida e eficiente para QC e pré-processamento

## Passo 1: Preparar o Ambiente

```bash
micromamba activate curso
cd /workspaces/curso_genomica_viral
```

## Passo 2: Entender os Parâmetros do fastp

Veja a documentação do fastp:

```bash
fastp --help
```

### Parâmetros que utilizaremos

- `-i/I`: Arquivos de entrada (R1 e R2)
- `-o/O`: Arquivos de saída (R1 e R2)
- `-l`: Comprimento mínimo de reads
- `--detect_adapter_for_pe`: Detecção automática de adaptadores
- `--cut_front`: Mova uma janela deslizante da frente (5') para a cauda, descartando as bases na janela se sua qualidade média for menor que o limiar.
- `--cut_tail`: Mova uma janela deslizante da cauda (3') para a frente, descartando as bases na janela se sua qualidade média for menor que o limiar.
- `--cut_mean_quality`: Qualidade média mínima para a janela.
- `-h/-j`: Relatórios HTML e JSON

## Passo 3: Executar o fastp

Criando diretório de output

```bash
mkdir -p outputs/01_qc
```

```bash
fastp \
    -i inputs/Art_R1.fastq.gz \
    -I inputs/Art_R2.fastq.gz \
    -o outputs/01_qc/Art.clean_R1.fastq.gz \
    -O outputs/01_qc/Art.clean_R2.fastq.gz \
    -h outputs/01_qc/Art.fastp.html \
    -j outputs/01_qc/Art.fastp.json \
    --detect_adapter_for_pe \
    --thread 2 \
    -l 150 \
    --cut_front \
    --cut_tail \
    --cut_mean_quality 30
```

## Interpretação dos Resultados

### Relatório fastp (HTML)

- **Before filtering**: Qualidade antes do processamento
- **After filtering**: Qualidade após filtragem
- **Filtering result**: Estatísticas de reads removidos
- **Insert size**: Distribuição do tamanho de insertos

### Métricas Importantes

- **Q20 bases**: % de bases com qualidade ≥ 20 (99% correto)
- **Q30 bases**: % de bases com qualidade ≥ 30 (99.9% correto)
- **GC content**: Conteúdo GC (esperado: ~50% para Chikungunya)

## Próximos Passos

Com o controle de qualidade finalizado, prossiga para:
- [Módulo 02: Mapeamento e Montagem por Referência](../02_mapping/)

## Recursos Adicionais

- [fastp](https://github.com/OpenGene/fastp)
