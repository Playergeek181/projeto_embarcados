# Documentação para Compilar e Buildar Projeto na Raspberry Pi

Esta documentação resume os passos realizados para compilar e executar um projeto na Raspberry Pi conectado a um motor Maxon via RS-232. Ela inclui os comandos e procedimentos essenciais.

---

## 1. **Preparar o Ambiente de Desenvolvimento**

### Instalar Dependências Necessárias
Na Raspberry Pi:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential cmake g++ libarmadillo-dev pkg-config -y
```

---

## 2. **Transferir o Projeto para a Raspberry Pi**
No computador local, usar o `scp` para copiar o diretório do projeto para a Raspberry Pi:
```bash
scp -r /caminho/do/projeto pi@<IP-da-Raspberry>:/home/pi/<destino-na-raspberry>
```
Exemplo:
```bash
scp -r build_arm pi@192.168.113.106:/home/pi/Embarcados/ProjetoCerto/projeto_embarcados
```

---

## 3. **Configurar e Compilar o Projeto na Raspberry Pi**

### Acessar o Diretório do Projeto
```bash
cd /home/pi/Embarcados/ProjetoCerto/projeto_embarcados
```

### Configurar o Build com CMake
Criar um diretório de build e configurar o projeto:
```bash
mkdir build && cd build
cmake ..
```

### Compilar o Projeto
```bash
make
```

---

## 4. **Testar o Executável**
Após a compilação, um executável será gerado. Teste-o:
```bash
./eesc-aero-embedded-systems
```

Para rodar o executável com parâmetros (exemplo):
```bash
./eesc-aero-embedded-systems can0 dlqr constant-zero
```

---

## 5. **Configurar RS-232 na Raspberry Pi**

### Verificar Porta Serial Disponível
```bash
ls /dev/tty*
```

### Testar Comunicação com o Dispositivo via Minicom
Instalar o Minicom:
```bash
sudo apt install minicom -y
```

Conectar ao dispositivo:
```bash
sudo minicom -b 9600 -o -D /dev/ttyUSB0
```
Substitua `/dev/ttyUSB0` pela porta serial detectada.

### Configurar Permissões da Porta Serial
Adicione o usuário ao grupo `dialout`:
```bash
sudo usermod -aG dialout pi
sudo reboot
```

---

## 6. **Resolver Problemas Comuns**

### Erro: "cannot execute: required file not found"
Verifique se o executável é compatível com a arquitetura da Raspberry Pi (ARM):
```bash
file ./eesc-aero-embedded-systems
```
Caso o executável seja para x86\_64, recompile o projeto diretamente na Raspberry Pi.

### Erro: "std::system_error: CanController: No such device"
Certifique-se de que o dispositivo CAN ou RS-232 está conectado e configurado corretamente. Teste a conexão com o `minicom` antes de rodar o executável.

---

## 7. **Considerações Finais**
Esta documentação cobre os passos básicos para compilar e executar o projeto. Certifique-se de ajustar os comandos e configurações conforme as especificidades do seu hardware e software.

