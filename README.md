# AnaliseARP-CPT


#  Análise de ARP — Resolução de IP para MAC

> Laboratório prático desenvolvido no Cisco Packet Tracer para compreender e observar como o protocolo ARP funciona em uma rede local.

---

##  Sobre o projeto

Neste projeto, montei uma pequena rede no Cisco Packet Tracer para entender, na prática, como um computador consegue descobrir o endereço MAC de outro dispositivo quando conhece apenas o endereço IP.

A ideia foi observar o que acontece quando o PC0 precisa se comunicar com o PC1, mas ainda não possui o MAC correspondente ao IP de destino.

Durante o laboratório, acompanhei o processo de resolução de endereços, observei os pacotes no Simulation Mode e utilizei o comando `ping` para verificar se a comunicação estava funcionando.

---

##  Objetivos

- Compreender a finalidade do protocolo ARP.
- Observar como um endereço IP é associado a um endereço MAC.
- Identificar o que acontece durante um ARP Request e um ARP Reply.
- Entender o uso de broadcast na descoberta do endereço MAC.
- Observar a tabela ARP antes e depois da comunicação.
- Relacionar ARP com a comunicação Ethernet.
- Validar a conectividade entre os dispositivos utilizando `ping`.

---

##  Ferramentas utilizadas

- Cisco Packet Tracer
- IPv4
- Ethernet
- ARP
- ICMP
- Command Prompt
- Simulation Mode

---

##  Topologia

A rede foi montada com dois computadores conectados a um switch.

PC0 → Switch0 → PC1

### Dispositivos

| Dispositivo | Endereço IPv4 | Máscara de sub-rede |
|-------------|---------------|---------------------|
| PC0 | 10.0.0.10 | 255.255.255.0 |
| PC1 | 10.0.0.20 | 255.255.255.0 |

Os dois computadores pertencem à mesma rede:

10.0.0.0/24

Por estarem na mesma rede local, a comunicação entre eles não precisou passar por um roteador.

---

##  O que é ARP?

ARP significa **Address Resolution Protocol**.

Ele é utilizado em redes IPv4 para descobrir qual endereço MAC está associado a determinado endereço IP dentro de uma rede local.

Antes de enviar um quadro Ethernet, o computador precisa saber para qual endereço MAC deve encaminhá-lo.

Por exemplo, o PC0 conhece o IP do PC1:

10.0.0.20

Mas ainda precisa descobrir:

MAC = ?

O ARP permite realizar essa associação:

10.0.0.20 → MAC correspondente

Depois que o endereço é descoberto, o computador pode utilizá-lo na comunicação Ethernet.

---

##  ARP Request

Quando o PC0 precisa se comunicar com o PC1 e ainda não conhece o MAC correspondente, ele envia um **ARP Request**.

Essa solicitação pode ser entendida como:

> "Quem possui o endereço IP 10.0.0.20?"

O ARP Request é enviado utilizando **broadcast** na rede local.

O endereço MAC de broadcast utilizado é:

FF:FF:FF:FF:FF:FF

Isso permite que os dispositivos da rede recebam a solicitação e verifiquem se o IP procurado pertence a eles.

### Fluxo simplificado

PC0 → ARP Request → Switch → PC1

---

##  ARP Reply

Ao receber o ARP Request, o PC1 identifica que o endereço IP solicitado pertence a ele.

Então, responde ao PC0 com um **ARP Reply**, informando seu endereço MAC.

No laboratório, o PC0 aprendeu a seguinte associação:

10.0.0.20 → 0001.64ec.b6b0

A partir desse momento, o PC0 passa a conhecer o endereço MAC necessário para enviar os quadros Ethernet destinados ao PC1.

---

##  Tabela ARP

Uma das partes que mais gostei de observar foi a diferença entre a tabela ARP antes e depois da comunicação.

### Antes do ping

No PC0, executei:

    arp -a

O resultado foi:

    No ARP Entries Found

Isso mostrava que o PC0 ainda não possuía uma entrada ARP para o PC1.

### Depois do ping

Em seguida, executei:

    ping 10.0.0.20

Após a comunicação, consultei novamente a tabela:

    arp -a

E apareceu a associação:

    Internet Address    Physical Address
    10.0.0.20           0001.64ec.b6b0

Essa foi a evidência de que o PC0 aprendeu o MAC correspondente ao IP do PC1.

---

##  Teste de conectividade

Para verificar se os computadores conseguiam se comunicar, utilizei o comando:

    ping 10.0.0.20

O resultado foi:

    Packets: Sent = 4
    Packets: Received = 4
    Lost = 0 (0% loss)

### Resultado

- 4 pacotes enviados
- 4 pacotes recebidos
- 0% de perda

A comunicação entre PC0 e PC1 foi realizada com sucesso.

---

##  Observação no Simulation Mode

Utilizei o **Simulation Mode** do Cisco Packet Tracer para acompanhar o fluxo dos pacotes durante a comunicação.

A sequência observada foi:

1. ARP Request
2. ARP Reply
3. ICMP Echo Request
4. ICMP Echo Reply

### ARP Request

O PC0 procura o MAC associado ao IP do PC1.

PC0 → Broadcast → Rede local

### ARP Reply

O PC1 responde informando seu endereço MAC.

PC1 → PC0

### ICMP Echo Request

Depois que o PC0 conhece o MAC do PC1, o `ping` pode prosseguir.

PC0 → PC1

### ICMP Echo Reply

O PC1 responde ao pedido de ICMP.

PC1 → PC0

---

##  Fluxo completo

O funcionamento observado no laboratório pode ser resumido assim:

PC0 conhece o IP de destino: 10.0.0.20

↓

PC0 verifica sua tabela ARP.

↓

O MAC ainda não foi encontrado.

↓

PC0 envia um ARP Request:

"Quem possui 10.0.0.20?"

↓

A solicitação é enviada em broadcast.

↓

PC1 reconhece seu próprio IP.

↓

PC1 envia um ARP Reply informando seu MAC.

↓

PC0 aprende a associação:

10.0.0.20 → 0001.64ec.b6b0

↓

A comunicação ICMP pode prosseguir.

↓

Ping realizado com sucesso.

---

##  Evidências

As evidências do laboratório demonstram:

### 01 — Topologia

Rede composta por dois computadores conectados a um switch.

### 02 — Tabela ARP inicial

O PC0 inicialmente não possuía uma entrada para o PC1.

### 03 — ARP Request

Visualização da solicitação enviada pelo PC0 para descobrir o MAC associado ao IP 10.0.0.20.

### 04 — ARP Reply

Resposta do PC1 informando seu endereço MAC.

### 05 — Tabela ARP após a comunicação

Associação aprendida pelo PC0:

10.0.0.20 → 0001.64ec.b6b0

### 06 — Teste de conectividade

Ping realizado com 4 pacotes enviados, 4 recebidos e 0% de perda.

---

##  Conceitos praticados

- IPv4
- Máscara de sub-rede
- Rede local (LAN)
- Ethernet
- Endereço MAC
- Endereço IPv4
- ARP
- ARP Request
- ARP Reply
- Broadcast
- Unicast
- Tabela ARP
- ICMP
- `ping`
- Cisco Packet Tracer
- Simulation Mode

---

##  O que eu aprendi

Este laboratório me ajudou a entender melhor como acontece a comunicação dentro de uma rede local.

Antes de realizar a atividade, eu já sabia que o ARP era utilizado para descobrir o MAC a partir de um IP. Mas observar esse processo acontecendo no Packet Tracer tornou o conceito muito mais claro.

Pude perceber que:

- O computador pode conhecer o IP de destino, mas ainda não conhecer o MAC.
- O ARP Request é utilizado para descobrir quem possui determinado IP.
- O ARP Request é enviado em broadcast.
- O ARP Reply informa o endereço MAC do dispositivo que possui o IP procurado.
- A associação entre IP e MAC é armazenada na tabela ARP.
- Depois que o MAC é conhecido, a comunicação Ethernet pode prosseguir.
- O `ping` utiliza ICMP para testar a conectividade.
- O Simulation Mode permite visualizar o caminho dos pacotes e entender melhor o que acontece por trás de uma comunicação aparentemente simples.

---

##  Conclusão

Este laboratório foi importante para transformar um conceito teórico em uma experiência prática.

Ao observar a comunicação entre os dois computadores, consegui entender melhor a relação entre **IP, MAC, ARP e Ethernet**.

O PC0 conhecia o endereço IP do PC1, mas precisava descobrir o MAC correspondente para conseguir enviar os quadros Ethernet.

Depois do processo de ARP, a associação foi aprendida e a comunicação foi realizada com sucesso.

O resultado do `ping`, com 0% de perda, confirmou que os dispositivos estavam conseguindo se comunicar.

---

### 🛠️ Ambiente

**Ferramenta:** Cisco Packet Tracer  
**Área:** Networking / Cybersecurity  
**Tipo:** Laboratório prático  
**Protocolo principal:** ARP  
**Protocolo de teste:** ICMP
