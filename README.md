# Objetivo do Projeto

Projeto da materia SAA0356 - Sistemas Embarcados para Veículos Aéreos, ofereciada pelo departamento de aeronáutica da escola de engenharia de São Carlos e ministrada pelo Professor Doutor Glauco Caurin. O objetivo do trabalho é o desenvolvimento de uma comunicação entre o sistema EPOS P da Maxon Motors e um sistema ARM utilizando o protocolo Open CAN, para controle e posicionamento de um motor elétrico com encoder incremental.

## Como instalar o projeto em um sistema **computador** com sistema x86_64

### Etapa 1: Instalação das Bibliotecas Lely-core

1. Certifique-se de ter as ferramentas de desenvolvimento instaladas em sua máquina:

   - **GNU Build System** (configure, make, make install)
   - **autotools** (autoconf, automake e libtool)
   - **Toolchain**:


   ```bash
   sudo apt-get install crossbuild-essential-arm64
   ```

3. Clone o repositório da Lely-core na pasta `dockerfile` e acesse o diretório clonado:

   ```bash
   cd /dockerfile
   git clone https://gitlab.com/lely_industries/lely-core.git
   cd lely-core
   ```

4. Configure e compile as bibliotecas em C++ para a arquitetura ARM:

   ```bash
   autoreconf -i
   mkdir -p build && cd build
   ./configure --disable-cython
   make
   sudo make install
   ```

### Etapa 2: Criação do Docker

1. Retorne ao diretório do `dockerfile` e construa o container Debian usando o `Dockerfile`:

   ```bash
   cd ../../
   docker build . -t build_manopla -f ./Dockerfile
   ```

2. Depois que o container for criado com sucesso, copie o projeto inteiro para uma pasta chamada `projeto` e inicie o container:

   ```bash
   cd ..
   docker run --rm -it -v $(pwd):/projeto build_manopla bash
   ```

   > Este processo pode levar algum tempo dependendo do desempenho da sua máquina. Ao final, o container será aberto e você poderá trabalhar dentro dele.

### Etapa 3: Compilação Cruzada do Código Fonte

1. Dentro do container, acesse o diretório `projeto` e compile o código para a arquitetura `arm64`:

   ```bash
   cd /projeto
   mkdir build_arm && cd build_arm
   cmake -DARM_TARGET=1 ..
   make
   ```

2. Verifique se a compilação foi bem-sucedida utilizando o comando abaixo para inspecionar o executável gerado:

   ```bash
   file eesc-aero-embedded-systems
   ```

   Saída esperada:

   ```
   eesc-aero-embedded-systems: ELF 32-bit LSB pie executable, ARM,
   EABI5 version 1 (GNU/Linux), dynamically linked, interpreter
   /lib/ld-linux-armhf.so.3, BuildID[sha1]=4b45a7d6be21fbbb5549f8df6f8c295996a03a85,
   for GNU/Linux 3.2.0, with debug_info, not stripped
   ```

3. Para transferir o executável ao sistema embarcado, conecte ambos os dispositivos à mesma rede e obtenha o IP e o usuário do dispositivo de destino. Use o comando `scp` para realizar a transferência:

   ```bash
   scp [source username@IP]:/[directory and file name] [destination username@IP]:/[destination directory]
   ```

   Exemplo prático:

   Enviar o arquivo `eesc-aero-embedded-systems` para a pasta `Documents` da Raspberry no endereço IP `192.168.0.15` com o usuário `glauco`:

   ```bash
   scp ./eesc-aero-embedded-systems glauco@192.168.0.15:~/Documents
   ```

## Como instalar o projeto em um sistema **Raspberry Pi** com sistema ARM

### Etapa 1: Preparar o Ambiente de Desenvolvimento

1. Instalar Dependências Necessárias
Na Raspberry Pi:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential cmake g++ libarmadillo-dev pkg-config -y
```

### Etapa 2: Configurar e Compilar o Projeto na **Raspberry Pi**

1. Acessar o Diretório do Projeto
```bash
cd /home/pi/Embarcados/ProjetoCerto/projeto_embarcados
```

2. Configurar o Build com CMake
Criar um diretório de build e configurar o projeto:
```bash
mkdir build && cd build
cmake ..
```

3. Compilar o Projeto
```bash
make
```

### Etapa 3: Testar o Executável      

Foi utilizado o cabo de conexão RS-232 com um adapatador CH340, porém por conta do codigo ser para o protocolo CAN não foi possivel testar.

Após a compilação, um executável será gerado. Teste-o:
```bash
./eesc-aero-embedded-systems
```

Rodando o seguinte executavel sem o sistema CAN conectado retorna o seguinte erro `MISSING can0`

Para rodar o executável com parâmetros (exemplo):
```bash
./eesc-aero-embedded-systems can0 dlqr constant-zero
```



---

   # Team 
| Photo | Name | Contribution | GitHub | LinkedIn | Email |
|-------|------|--------------|--------|----------|-------|
| <img src="https://github.com/Playergeek181.png" width="80"> | João Aires Corrêa Fernandes Marsicano | Code development and documentation | [GitHub](https://github.com/Playergeek181) | [LinkedIn](https://www.linkedin.com/in/joao-aires-marsicano/) | joao.aires.marsicano@usp.br |
| <img src="https://github.com/Rem-Cap.png" width="80"> | Renan Ribeiro Machado | Code development and Hardware Testing | [GitHub](https://github.com/Rem-Cap) | [LinkedIn](https://www.linkedin.com/in/renan-machado-95178a23b/) | renan_11@usp.br |
> All members are students of Mechatronic Engineering at the University of São Paulo, School of Engineering in São Carlos.
