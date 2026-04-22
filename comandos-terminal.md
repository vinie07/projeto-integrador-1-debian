# Monitoramento e Diagnóstico no Linux

## Referências

- [5 Comandos Linux para Monitorar o Uso da CPU – Anderson Tecnologia](https://sites.google.com/view/andersontecnologia/ubuntu/5-comandos-linux-para-monitorar-o-uso-da-cpu)
- [What tools are there for monitoring CPU? – r/linux4noobs](https://www.reddit.com/r/linux4noobs/comments/bsjzws/what_tools_are_there_on_linux_for_monitoring_cpu/?tl=pt-br)
- [Linux Processor/CPU/Memory Information Commands – Alvin Alexander](https://alvinalexander.com/linux-unix/linux-processor-cpu-memory-information-commands/)
- [Monitor CPU/GPU Temp – It's FOSS](https://itsfoss.com/monitor-cpu-gpu-temp-linux/)
- [How to check video memory size – Ask Ubuntu](https://askubuntu.com/questions/46197/how-to-check-video-memory-size)
- [Check size of physical memory (RAM) – 2daygeek](https://www.2daygeek.com/easy-ways-to-check-size-of-physical-memory-ram-in-linux/)
- [Command-line tools to monitor Linux performance – TecMint](https://www.tecmint.com/command-line-tools-to-monitor-linux-performance/)
- [Linux CPU load stress test with stress-ng – TecMint](https://www.tecmint.com/linux-cpu-load-stress-test-with-stress-ng-tool/)
- [Informações de memória e CPU no Linux – Medium](https://medium.com/@habbema/informa%C3%A7%C3%B5es-de-mem%C3%B3ria-e-cpu-no-linux-8793edcaed77)
- [5 maneiras de verificar informações da CPU no Linux – SempreUpdate](https://sempreupdate.com.br/5-maneiras-de-verificar-as-informacoes-da-cpu-no-linux/)
- [Vídeo – YouTube](https://www.youtube.com/watch?v=9cg6zjOC29c)
- ⭐ [HowToIdentifyADevice – Debian Wiki (pt_BR)](https://wiki.debian.org/pt_BR/HowToIdentifyADevice)
- [Analisar o tempo de boot dos serviços no Linux – Mateus Muller](https://mateusmuller.me/2018/05/22/analisar-o-tempo-de-boot-dos-servicos-no-linux/)
- `man proc`

---

## CPU

```bash
cat /proc/cpuinfo          # informações gerais da CPU
cat /proc/cpuinfo | more   # paginado
cat /proc/cpuinfo | grep processor | wc -l      # contar núcleos
top -bn1 | grep "Cpu(s)"   # uso atual da CPU (snapshot único, não interativo)
ps aux                     # processos em execução com uso de CPU
lscpu                      # resumo da arquitetura da CPU
sudo lshw -class CPU       # detalhes de hardware da CPU
hwinfo --cpu               # informações detalhadas via hwinfo
cpu-info                   # informações da CPU
```

---

## Memória (RAM)

```bash
cat /proc/meminfo          # informações brutas de memória
vmstat 1 10                # estatísticas de memória a cada 1s, 10 vezes
free -h                    # memória total, usada e livre (formato legível)
free -m                    # uso de RAM em megabytes
free -g                    # uso de RAM em gigabytes
```

> Para uma visão mais **visual** no terminal, use `htop` (ver seção [Monitoramento em Tempo Real](#monitoramento-em-tempo-real)).

---

## Tempo de Boot

```bash
systemd-analyze                        # resumo do tempo de boot
systemd-analyze blame                  # serviços ordenados pelo tempo de inicialização
systemd-analyze blame | head           # apenas os mais lentos (topo da lista)
systemd-analyze blame | tail           # apenas os mais rapidos (cauda)
systemd-analyze critical-chain         # tempo exato dos serviços mais críticos
systemd-analyze plot > report.svg      # ⭐ gera gráfico visual do boot em .SVG

sudo systemctl disable <service-name>  # desabilitar serviço

```

**DICA:** Ao analisar o tempo de boot, é possivel desabilitar um serviço "famoso" por causar demora no boot. 

**NetworkManager-wait-online.service**

Para desabilitar: **sudo systemctl disable NetworkManager-wait-online.service**

---

## Disco

```bash
df -h              # lista os discos montados com uso em formato legível
iostat -d -h       # taxa de leitura/escrita por dispositivo de disco
```

> Se `iostat` não estiver disponível: `sudo apt install sysstat`

---

## Tempo de Instalação de Pacote

```bash
time sudo apt install <pacote>
# Se já estiver instalado, forçar reinstalação para medir:
time sudo apt install --reinstall <pacote>
```

Saída de exemplo:

```
real    0m4.312s
user    0m1.204s
sys     0m0.387s
```

| Campo  | Significado |
|--------|-------------|
| `real` | Tempo total decorrido no relógio (o mais relevante) |
| `user` | Tempo de CPU em modo usuário |
| `sys`  | Tempo de CPU em modo kernel (sistema) |


---

## Benchmark / Stress Test

```bash
s-tui       # monitor interativo com stress test integrado (TUI)
stress      # stress test simples
stress-ng   # stress test mais completo e configurável

# sysbench — teste de CPU com controle de threads
sudo apt install sysbench
sysbench cpu --threads=2 run   # roda benchmark de CPU com 2 threads
```

---

## Temperatura

```bash
watch -n 2 sensors          # temperatura dos sensores (CPU, etc.) atualizada a cada 2s
watch -n 2 nvidia-smi       # temperatura da GPU NVIDIA atualizada a cada 2s
# Para AMD: verificar uso do MESA (comando equivalente a confirmar)
```

---

## GPU / Memória de Vídeo

```bash
glxinfo | egrep -i 'device|memory'   # RAM e dispositivo da GPU
```

---

## Hardware Geral

```bash
sudo lshw          # listagem completa de hardware
sudo lshw | more   # paginado
inxi               # resumo de hardware geral
neofetch           # informações do sistema (visual/bonito)
dmidecode          # informações avançadas do hardware via DMI/SMBIOS
hwinfo             # detalhes completos de hardware
py-cpuinfo         # informações da CPU via Python
lsusb              # dispositivos USB conectados
lspci              # dispositivos PCI
hardinfo           # ⭐ interface grafica geral com benchmark

```

---

## Monitoramento em Tempo Real

```bash
nmon        # ⭐ monitor interativo completo (CPU, RAM, disco, rede)
glances     # similar ao htop, com mais informações
htop        # monitor de processos interativo (mais visual que top)
```

---

## Rede

```bash
netstat       # estatísticas de rede (conexões, portas)
iotop         # I/O por processo (equivalente ao htop para disco/rede)
iostat        # estatísticas de CPU + dispositivos de bloco (HDs)
nethogs       # tráfego de rede por processo
```

---

## Kernel / Sistema

```bash
dmesg         # mensagens do kernel (diagnóstico avançado)
```
