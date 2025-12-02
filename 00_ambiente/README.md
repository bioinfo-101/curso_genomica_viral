## 0 O ambiente!

Uma atividade de rotina na bioinformática é preparar seu ambiente de análise. Neste ambiente é onde podemos instalar ferramentas e bibliotecas em versões específicas, é uma forma de garantir a reprodutibilidade das nossas análises.

Temos várias formas de fazer isso, como containers por exemplo que são ambientes isolados que podem ser executados em qualquer máquina que tenha o docker ou singularity configurados.

Outra forma é usar ambientes virtuais do python (aka virtualenvs, ou venvs!), ou ambientes conda. Neste curso vamos utilizar o gerenciador micromamba, que permite gerenciar ambientes conda de forma rápida e eficiente.

É recomendado utilizar o ambiente do codespaces do github para realizar as atividades deste curso. Dessa forma, as linhas de comando abaixo estão definidas para um ambiente "zerado", como no caso do codespaces.

Vale ressaltar que essa etapa de configuração do ambiente com micromamba precisa ser realizada apenas uma vez no seu ambiente.

## Objetivos

- Entender como funciona o micromamba
- Aprender a criar e gerenciar ambientes conda
- Aprender a base sobre o versionamento semântico

### Instalando dependencias (curl) para instalar o micromamba

```bash
sudo apt update -y && sudo apt upgrade -y && sudo apt install curl
```

### Instalando o micromamba

```bash
curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/1.5.7 | tar -xvj bin/micromamba
./bin/micromamba shell init -s bash -p ~/micromamba
source ~/.bashrc
micromamba activate
```

### Criando o ambiente conda

```bash
cd 00_ambiente
micromamba env create -f environment.yaml -y
micromamba activate curso
```

### Checando

Você pode checar se as instalações deram certas verificando a versão de alguma ferramenta e comparando com a versão setada no `environment.yaml`. Por exemplo:

```bash
fastp --version
```

Informa: `fastp 1.0.1`, no arquivo `environment.yaml` está definido `fastp=1.0`, portanto a versão instalada está correta, como não definimos a especificação da versão `patch` (detalhes na documentação[ sobre versionamento semântico](https://semver.org/lang/pt-BR/)) o micromamba irá instalar a versão mais recente do `fastp` que seja `>=1.0,<1.1`.

### Algumas observações

- Quando trabalhamos em ambiente de terminal, é importante ter certeza que você está no diretório certo, no comando acima, passamos como argumento o arquivo environment.yaml que está dentro do diretório 00_ambiente que por sua vez está dentro do diretório curso_genomica_viral.
- O micromamba vai criar um ambiente conda chamado curso, que vai conter todas as dependências necessárias para realizar as análises do curso. O argumento -y garante que todas as dependências sejam instaladas sem precisar confirmar digitando `y` de forma interativa no terminal.

## Próximos Passos

Com o ambiente preparado, prossiga para:
- [Módulo 01: Controle de qualidade](../01_qc/)

## Recursos Adicionais

- [Micromamba](https://github.com/mamba-org/mamba)
- [SemVer](https://semver.org/lang/pt-BR/)
