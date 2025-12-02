# Módulo 02: Mapeamento e Montagem por Referência

## Objetivos

- Compreender o processo de montagem por referência
- Mapear reads com BWA-MEM
- Processar alinhamentos com SAMtools
- Gerar sequência consenso com iVar

## Ferramentas

- **BWA**: Burrows-Wheeler Aligner para mapeamento
- **SAMtools**: Manipulação de arquivos SAM/BAM
- **iVar**: Chamada de consenso e variantes virais

## Fluxo de Trabalho

```
Referência → BWA align → SAMtools sort/index → iVar consensus → FASTA
```

## Passo 1: Preparar o Ambiente

```bash
micromamba activate curso
cd /workspaces/curso_genomica_viral/02_mapping
```

## Passo 2: Indexar a Referência

Esta etapa precisa ser realizada apenas uma vez. Conforme apresentado na parte teórica, o index é uma estrutura de dados que permite que o alinhamento seja feito de forma rápida e eficiente.

```bash
bwa index reference.fa
```

## Passo 3: Executar Mapeamento

Aqui utilizaremos os reads tratados com o fastp, presentes no diretório `01_qc` dentro de `outputs`.

```bash
mkdir -p ../outputs/02_mapping

bwa mem \
    -t 2 \
    -o ../outputs/02_mapping/Art.sam \
    reference.fa \
    ../outputs/01_qc/Art.clean_R1.fastq.gz \
    ../outputs/01_qc/Art.clean_R2.fastq.gz 
```

## Passo 4: Realizar a ordenação e compactação com samtools

É comum que ferramentas de bioinformática trabalhem com o arquivo sorted.bam que não é nada mais nada menos que os reads mapeados contra o genoma de referência ordenados por coordenadas e compactados.

```bash
samtools sort -o ../outputs/02_mapping/Art.sorted.bam ../outputs/02_mapping/Art.sam
```

## Passo 5: Geração de Consenso com iVar

Diferente das estratégias tradicionais para geração de consenso (rodar uma ferramenta para chamar variantes, outra para detectar as regiões de baixa cobertura e outra para gerar o consenso) aqui usaremos o iVar para realizar todas as etapas de uma vez.

### Parâmetros que utilizaremos

#### Samtools mpileup

- aa: Coloca no output todas as posições, inclusive com 0 de profundidade
- d: Máximo de profundidade considerado para evitar uso excesivo de memória

#### iVar consensus

- `-t`: Frequência mínima para chamada de SNV (0 = base em maior frequência)
- `-m`: Profundidade mínima (default: 10, passaremos 20)
- `-q`: Qualidade mínima de base (default: 20, passaremos 30)
- `-n`: Símbolo para regiões de baixa cobertura (N)
- `-c`: Frequência mínima para chamada de indel (default: 0.8, passaremos 0.51)
- `-p`: Prefixo para o arquivo de saída

```bash
samtools mpileup \
    -aa \
    -d 2000 \
    --reference reference.fa \
    ../outputs/02_mapping/Art.sorted.bam | \
        ivar consensus \
            -p ../outputs/02_mapping/Art \
            -q 30 \
            -t 0 \
            -m 20 \
            -n N \
            -c 0.51
```

Verificando o nome da amostra gerada com:

```bash
grep ">" ../outputs/02_mapping/Art.fa
```

Podemos notar que a amostra se chama `Consensus_Art_threshold_0_quality_30`, que é o nome que o iVar atribui para a amostra. Vamos renomear para ter um nome mais limpo nas nossas próximas análises.

```bash
sed -i -e "s/>.*/>Art/g" ../outputs/02_mapping/Art.fa
```

## Próximos Passos

Com o consenso gerado, prossiga para:
- [Módulo 03: Análise com Nextclade](../03_nextclade/)

## Recursos Adicionais

- [BWA](https://github.com/lh3/bwa)
- [SAMtools](https://github.com/samtools/samtools)
- [iVar](https://github.com/ivar-core/ivar)
