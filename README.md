# Execução do Projeto

## Etapa 1: Instalação das Bibliotecas Lely-core

1. Certifique-se de ter as ferramentas de desenvolvimento instaladas em sua máquina:
   - **GNU Build System** (configure, make, make install)
   - **autotools** (autoconf, automake e libtool)
   - **Toolchain**:

   ```bash
   sudo apt-get install crossbuild-essential-arm64
   ```

2. Clone o repositório da Lely-core na pasta `dockerfile` e acesse o diretório clonado:

   ```bash
   cd /dockerfile
   git clone https://gitlab.com/lely_industries/lely-core.git
   cd lely-core
   ```

3. Configure e compile as bibliotecas em C++ para a arquitetura ARM:

   ```bash
   autoreconf -i
   mkdir -p build && cd build
   ./configure --disable-cython
   make
   sudo make install
   ```

## Etapa 2: Criação do Docker

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

## Etapa 3: Compilação Cruzada do Código Fonte

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
