#   Monitoramento e Diagnóstico no Linux

##  Referências:

* [5 Comandos Linux para Monitorar o Uso da CPU – Anderson Tecnologia](https://sites.google.com/view/andersontecnologia/ubuntu/5-comandos-linux-para-monitorar-o-uso-da-cpu)
* [What tools are there for monitoring CPU? – r/linux4noobs](https://www.reddit.com/r/linux4noobs/comments/bsjzws/what_tools_are_there_on_linux_for_monitoring_cpu/?tl=pt-br)
* [Linux Processor/CPU/Memory Information Commands – Alvin Alexander](https://alvinalexander.com/linux-unix/linux-processor-cpu-memory-information-commands/)
* [Monitor CPU/GPU Temp – It's FOSS](https://itsfoss.com/monitor-cpu-gpu-temp-linux/)
* [How to check video memory size – Ask Ubuntu](https://askubuntu.com/questions/46197/how-to-check-video-memory-size)
* [Check size of physical memory (RAM) – 2daygeek](https://www.2daygeek.com/easy-ways-to-check-size-of-physical-memory-ram-in-linux/)
* [Command-line tools to monitor Linux performance – TecMint](https://www.tecmint.com/command-line-tools-to-monitor-linux-performance/)
* [Linux CPU load stress test with stress-ng – TecMint](https://www.tecmint.com/linux-cpu-load-stress-test-with-stress-ng-tool/)
* [Informações de memória e CPU no Linux – Medium](https://medium.com/@habbema/informa%C3%A7%C3%B5es-de-mem%C3%B3ria-e-cpu-no-linux-8793edcaed77)
* [5 maneiras de verificar informações da CPU no Linux – SempreUpdate](https://sempreupdate.com.br/5-maneiras-de-verificar-as-informacoes-da-cpu-no-linux/)
* [Vídeo – YouTube](https://www.youtube.com/watch?v=9cg6zjOC29c)
* ⭐ [HowToIdentifyADevice – Debian Wiki (pt_BR)](https://wiki.debian.org/pt_BR/HowToIdentifyADevice)
* [Analisar o tempo de boot dos serviços no Linux – Mateus Muller](https://mateusmuller.me/2018/05/22/analisar-o-tempo-de-boot-dos-servicos-no-linux/)
* usar man para obter um manual do , exemplo `man btop`

---

## CPU:

```bash
cat /proc/cpuinfo          # informações gerais da CPU
cat /proc/cpuinfo | more   # paginado
cat /proc/cpuinfo | grep processor | wc -l # contar núcleos
top -bn1 | grep "Cpu(s)"   # uso atual da CPU (Não é )
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

Para uma visão mais **visual** no terminal, use `htop`

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
sudo iostat -d -h       # taxa de leitura/escrita por dispositivo de disco
```

Se `iostat` não estiver disponível: `sudo apt install sysstat`

---

## Tempo de Instalação de Pacote

```bash
time sudo apt install <pacote>

# Se já estiver instalado, forçar reinstalação para medir:
time sudo apt install --reinstall <pacote>
```

Saída de exemplo:

| Campo | Tempo    |                     Significado                    |
|-------|----------|----------------------------------------------------|
|`real` |  0m4.312s| Tempo total decorrido no relógio (o mais relevante |
|`user` |  0m1.204s| Tempo de CPU em modo usuário                       |
|`sys`  | 0m0.387s | Tempo de CPU em modo kernel (sistema		|

---

## Benchmark / Stress Test

### s-tui

Monitor interativo com stress test integrado

```bash
s-tui                    # modo interativo
s-tui -c ou --csv        # ⭐ modo CSV — exporta os dados em formato legivel para CSV
```

---

### stress

Teste de sistema simples.

```bash
stress -c 13 -i 4 -v -t 1m > bench-stress.txt
```

| Comando | Significado                              |
|------|------------------------------------------|
| `-v` | verbose (mais detalhes na saida)         |
| `-t` | tempo de duração (1m,30s...)         |
| `-c` | workers/processos de CPU usando `sqrt`   |
| `-i` | workers/processos usando `sync`          |
| `-m` | workers/processos usando `malloc`        |
| `-d` | workers/processos usando `write`/`unlink`|


---

### stress-ng

Mais completo e configuravel

```bash
stress-ng --cpu 0 --vm 2 --vm-bytes 512M --hdd 1 --io 4 --fork 8 --timeout 2m --metrics --times --perf --tz --verify -v > bench-stressng.txt
```

| Comando                  | Significado                                                |
|--------------------------|------------------------------------------------------------|
| `--cpu 0`                | processos de CPU proporcional ao número de núcleos         |
| `--vm 2 --vm-bytes 512M` | 2 processos fazendo teste de 512MB de escrita e leitura    |
| `--hdd 1`                | 1 processo de arquivo temporário em disco                  |
| `--io 4`                 | forçar buffer cache com 4 workers/processos                |
| `--fork 8`               | criar processos filhos e matar para sobrecarregar o kernel |
| `--perf`                 | métricas do kernel via perf                                |
| `--tz`                   | mostra temperatura durante o teste                         |
| `--verify`               | verifica se os resultados estão corretos                   |
| `-v`                     | mais detalhes na saída                                     |

---

### sysbench

| Comandos comuns:      | Significado                       |
|-----------------------|-----------------------------------|
| `--threads=N`         | número de threads                 |
| `--time=N`            | duração em segundos               |
| `--verbosity=N`       | nível de detalhe (0–5)            |
| `--report-interval=N` | exibe progresso a cada N segundos |

---

#### CPU

```bash
# básico
sysbench cpu --threads=2 run

# completo
sysbench cpu --threads=4 --time=60 --cpu-max-prime=20000 --report-interval=10 run
```

> `--cpu-max-prime=N` → calcula números primos até N (quanto mais, mais pesado)

---

#### Memoria

```bash
# escrita sequencial
sysbench memory --threads=4 --memory-block-size=1K --memory-total-size=6G --memory-oper=write --memory-access-mode=seq run

# leitura aleatória
sysbench memory --threads=4 --memory-block-size=1K --memory-total-size=6G --memory-oper=read --memory-access-mode=rnd run
```

`--memory-total-size` deve ser **maior que a RAM** para evitar que o sistema use cache e distorça o resultado.

`--memory-block-size=N` → tamanho do bloco (ex: `1K`)  

`--memory-oper=read/write` → operação a testar

---

#### FileIO

São **3** passos necessarios para a execução correta:

1. `prepare`
2. `run`
3. `cleanup`

```bash
# 1. preparar arquivos (ex: 5GB divididos em 5 arquivos)
sysbench fileio --file-total-size=5G --file-num=5 prepare

# 2. rodar teste de leitura/escrita aleatória
sysbench fileio --file-total-size=5G --file-num=5 \
  --file-test-mode=rndrw --threads=4 --time=60 run

# 3. limpar
sysbench fileio --file-total-size=5G --file-num=5 cleanup
```

`--file-extra-flags=direct` -> resultado mais real, usa I/O direto sem cache do SO

---

#### Comandos usados no Debian (configuração adotada):

Usar `--report-interval=1` para acompanhar via 1 segundo de atualização

```bash
# CPU
sysbench cpu --threads=3 --time=10 --cpu-max-prime=2000 --report-interval=1 run > cpu.txt

# RAM SEQUENCIAL
sysbench memory --threads=3 --memory-block-size=1K --memory-total-size=20G \
  --memory-oper=write --memory-access-mode=seq run > ram-write-seq.txt

# RAM ALEATORIA
sysbench memory --threads=3 --memory-block-size=1K --memory-total-size=20G \
  --memory-oper=read --memory-access-mode=rnd run > ram-read-rnd.txt

# FILEIO 1.PREPARE
sysbench fileio --file-total-size=5G --file-num=5 prepare > io-5g-5-prepare.txt

# FILEIO 2.RUN
sysbench fileio --file-total-size=5G --file-num=5 \
  --file-test-mode=rndrw --file-extra-flags=direct --threads=3 --time=10 run > io-5g-5-run.txt

# FILEIO 3.CLEANUP
sysbench fileio --file-total-size=5G --file-num=5 cleanup > io-5g-5-cleanup.txt
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
sudo dmidecode     # informações avançadas do hardware via DMI/SMBIOS
sudo hwinfo        # detalhes completos de hardware
py-cpuinfo         # informações da CPU via Python
lsusb              # dispositivos USB conectados
lspci              # dispositivos PCI
hardinfo2          # ⭐ interface grafica geral com benchmark
sudo grml-hwinfo   # ⭐ coleta TUDO de uma vez
```

---

## Monitoramento em Tempo Real

```bash
nmon        # ⭐ monitor interativo completo (CPU, RAM, disco, rede)
glances     # similar ao htop, com mais informações (visualmente mais simples
htop        # monitor de processos interativo (mais visual que top)
```

---

## Rede

```bash
netstat-nat   # estatísticas de rede (conexões, portas)
sudo iotop    # I/O por processo (equivalente ao htop para disco/rede)
iostat        # estatísticas de CPU + dispositivos de bloco (HDs)
sudo nethogs  # tráfego de rede por processo
```

---

## Kernel / Sistema

```bash
dmesg         # mensagens do kernel (diagnóstico avançado)
```



