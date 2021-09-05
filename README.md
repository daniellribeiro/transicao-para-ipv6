# transicao-para-ipv6
Esse tutorial tem o objetivo de auxiliar na execução dos testes práticos das técnicas de transição abaixo.
- Pilha Dupla
- Primeiramente será preciso baixar os programas abaixo:
- [Oracle VirtualBox](https://download.virtualbox.org/virtualbox/6.1.22/VirtualBox-6.1.22-144080-Win.exe)
- [Baixar VM](https://ipv6.br/downloads/CursoIPv6br-CORE4.6-20150318.ova)

## Importar Máquina Virtual
- Instalar o software Oracle VirtualBox
- Abrir o Oracle VirtualBox
- Clicar em Arquivo > Importar Appliance
- Selecionar a VM baixada, avançar os passos, e clicar em importar.

## Habilitar importar arquivos da Máquina Física para Máquina Virtual
- Clicar em iniciar, para abrir o ambiente que será usado nos testes
- Ir na aba Dispositivos > Área de Transferência Compartilhada > Bi-direcional
- Ir na aba Dispositivos > Arastar e Soltar > Bi-direcional

## Ajustar tamanho da tela
- Clicar em Dispositivos > Inserir imagem de CD dos adicionais para convidado
- Na VM > clicar em iniciar > File Manager > VBox_GAs_6.1.22
- Na VM > ir para Área de Trabalho > abrir Terminal Emulador
- Executar o comando abaixo:
```bash
sudo su -
```
O comando acima solitara uma senha, digitar **ipv6br**
- Executar o comando abaixo:
```bash
cd /media/VBox_GAs_6.1.22
./VBoxLinuxAdditions.run
reboot
```
- A VM reiniciará
- Na VM > clicar em iniciar > Settings > Display > em "Resolution" mudar para resolução do seu monitor

## Pilha Dupla
- Baixar arquivo pilha_dupla.imn
- Arrastar arquivo baixado para VM
- Abrir arquivo pilha_dupla.imn
- Clicar no Play
- Clicar duas vezes no pc3, para abrir o terminal
- Digitar: ```ping6 2001:db8:0::20```
- Esperar uns 5 segundos e parar execução
- Em packet loss deve aparecer 0%
- Digitar: ```ping 192.168.1.20```
- Esperar uns 5 segundos e parar execução
- Em packet loss deve aparecer 0%

## Túnel 6to4
- Baixar arquivo 6to4.imn
- Arastar arquivo baixado para VM
- Clicar no Play
- Clicar duas vezes no pc1
- Executar o comando abaixo:
```
ping6 2001:db8:0::20
```
- Em packet loss deve aparecer 100%
- Ainda no pc1, executar os comandos abaixo:
``` 
ip addr add 2002:CB00:7101::1 dev  lo
ip tunnel add to1234 mode sit ttl 64 remote 192.88.99.1 local 192.168.0.20
ip link set dev to1234 up
ip -6 route add 2002:CB00:7101::2 dev to1234
ip -6 route add ::/0 dev to1234
```
- No relay6to4 digitar os comandos abaixo:
```
ip addr add 2002:CB00:7101::2 dev lo
ip tunnel add toABCD mode sit ttl 64 remote 192.168.0.20 local 192.88.99.1
ip link set dev  toABCD up
ip -6 route add 2002:CB00:7101::1 dev toABCD
ping6 2002:CB00:7101::1
```
- Em packet loss deve aparecer 0%
- Clicar duas vezes no pc1
- Executar o comando abaixo:
```
ping6 2001:db8:0::20
```
- Em packet loss deve aparecer 0%
