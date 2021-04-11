---
title: Identificar as aplicações necessárias
description: Saiba mais sobre hardware e aparelhos virtuais para sensores certificados defender para sensores IoT e a consola de gestão no local.
ms.date: 01/13/2021
ms.topic: how-to
ms.openlocfilehash: 242e88e92d6197fd018c56fa55a4dd8166f5d027
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782763"
---
# <a name="identify-required-appliances"></a>Identificar as aplicações necessárias

Este artigo fornece informações sobre o Defender certificado para aparelhos de sensores IoT. O forte do defender IoT pode ser implantado em aparelhos físicos e virtuais.

Isto inclui aparelhos *pré-configurados* certificados, nos quais o software já está instalado, bem como aparelhos certificados não configurados nos quais pode descarregar e instalar o software necessário.

O artigo também fornece especificações para um aparelho de consola de gestão no local. A consola de gestão no local não está disponível como um aparelho pré-configurado.

- Se pretender adquirir um sensor pré-configurado, reveja os modelos disponíveis na secção [de aparelhos sensores](#sensor-appliances) e, em seguida, proceda à compra.

- Se pretender adquirir o seu próprio aparelho, reveja os modelos disponíveis na secção [de aparelhos Sensor](#sensor-appliances) e na secção de [aparelhos certificados Adicionais.](#additional-certified-appliances) Depois de adquirir o aparelho, pode descarregar e instalar o software.

- Se pretender adquirir a consola de gestão no local, reveja as informações na secção [de aparelhos de consola de gestão de consolas on-in.](#on-premises-management-console-appliance) Depois de adquirir o dispositivo, pode descarregar e instalar o software.

Depois de ter concluído as tarefas aqui, pode instalar o software e configurar a sua rede.

## <a name="sensor-appliances"></a>Aparelhos sensoriais

O Defender for IoT suporta implementações físicas e virtuais.

### <a name="physical-sensors"></a>Sensores físicos

Esta secção fornece uma visão geral dos modelos de sensores físicos que estão disponíveis. Pode adquirir sensores com software pré-configurado ou sensores de compra que não estejam pré-configurados.

| Tipo de implantação | Empresarial | Grandes Empresas | Montagem de cremalheira SMB| SMB acidentado|
|--|--|--|--|--|
| Imagem | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="O modelo de nível corporativo."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="O modelo de nível empresarial."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="O modelo de nível SMB."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="O modelo de nível robusto SMB."::: |
| Modelação | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| Portas de monitorização | Até 15 RJ45 ou 8 OPT | Até 8 RJ45 ou 6 OPT | 4 RJ45 | Até 5 |
| Largura de banda máxima [1](#anchortext) | 3 Gb/seg | 1 Gb/seg | 200 Mb/Sec | 100 Mb/seg |
| Dispositivos máximos protegidos | 30,000 | 15 000 | 1,000 | 800 |

Consulte [as especificações do aparelho](#appliance-specifications) para obter detalhes do fornecedor.

Sobre sensores pré-configurados: a Microsoft estabeleceu uma parceria com a Arrow para fornecer sensores pré-configurados. Para adquirir um sensor pré-configurado, contacte a Seta no seguinte endereço: <hardware.sales@arrow.com>

Sobre trazer o seu próprio aparelho: Reveja os modelos suportados aqui descritos. Depois de adquirir o seu aparelho, aceda ao **Defender para obter**  >  **sensores de rede IoT ISO**  >  **Installation** para descarregar o software.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Sensores de rede ISO.":::

<a id="anchortext">1</a> A capacidade de largura de banda pode variar, dependendo da distribuição dos protocolos.

### <a name="virtual-sensors"></a>Sensores virtuais

Esta secção fornece uma visão geral dos sensores virtuais que estão disponíveis.

| Tipo de implantação | Empresarial | Grandes Empresas | SMB |
|--|--|--|--|
| Largura de banda máxima | 2,5 Gb/seg | 800 Mb/seg | 160 Mb/seg |
| Dispositivos máximos protegidos | 30,000 | 10,000 | 2.500 |

## <a name="on-premises-management-console-appliance"></a>Aparelho de consola de gestão no local

A consola de gestão está disponível como uma implementação virtual.

| Tipo de implantação | Grandes Empresas |
|--|--|
| Tipo de aparelho | HPE DL20, VM |
| Número de sensores geridos | Até 300 |

Depois de adquirir uma consola de gestão no local, vá ao Defender para a consola de gestão **IoT**  >  **On-ins**  >  **ISO Installation** para descarregar a ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Consola de gestão no local.":::

## <a name="appliance-specifications"></a>Especificações do aparelho

Esta secção descreve especificações de hardware para os seguintes aparelhos:

- Implantação corporativa: HPE ProLiant DL360

- Implantação da empresa: HPE ProLiant DL20

- Implantação SMB: HPE ProLiant DL20

- Especificações do aparelho virtual

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Implantação corporativa: HPE ProLiant DL360

| Componente | Especificações técnicas |
|--|--|
| Chassis | Servidor de rack 1U |
| Dimensões | 42,9 x 43,46 x 70,7 (cm)/ 1,69" x 17,11" x 27,83" (in) |
| Peso | Máx 16,27 kg (35,86 lb) |
| Processador | Intel Xeon Silver 4215 R 3.2 GHz, cache 11M, 8c/16T, 130 W |
| Chipset | Intel C621 |
| Memória | 32 GB = 2 x 16-GB 2666MT/s DDR4 ECC UDIMM |
| Armazenamento | 6 x 1.2-TB SAS 12G Enterprise 10K SFF (2,5 in) em Hot-Plug Hard Drive - RAID 5 |
| Controlador de rede | Bordo: 2 x 1-Gb Broadcom BCM5720<br>LOM a bordo: cartão porta iDRAC 1-Gb Broadcom BCM5720<br><br>Externo: 1 x Adaptador de servidor Intel Ethernet i350 QP 1-Gb, Perfil Baixo |
| Gestão | HPE iLO Avançado |
| Acesso ao dispositivo | Duas USB traseiras 3.0<br>Uma frente USB 2.0<br>Um USB interno 3.0 |
| Energia | 2 x HPE 500 W Flex Slot Platinum Hot Plug Low Halogen Power Supply Kit |
| Suporte de cremalheira | HPE 1U Gen10 SFF Fácil instalar kit ferroviário |

### <a name="appliance-bom"></a>Aparelho BOM

| PN | Description | Quantidade |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO Server | 1 |
| P19766-B21 | Europa - Localização Multilíngue | 1 |
| P24479-L21 | Intel Xeon-S 4215 R FIO Kit para DL360 G10 | 1 |
| P24479-B21 | Kit de R Intel Xeon-S 4215 para DL360 Gen10 | 1 |
| P00922-B21 | Kit Inteligente HPE 16-GB 2Rx8 PC4-2933Y-R | 2 |
| 872479-B21 | HPE 1.2-TB SAS 10K SFF SC DS HDD | 6 |
| 811546-B21 | Adaptador HPE 1-GbE 4-p BASE-T I350 | 1 |
| P02377-B21 | HPE Smart Hybrid Capacitor w \_ cabo de 145 mm | 1 |
| 804331-B21 | Controlador HPE Smart Array P408i-a SR Gen10 | 1 |
| 665240-B21 | Adaptador HPE 1-GbE 4-p FLR-T I350 | 1 |
| 871244-B21 | HPE DL360 Gen10 Kit de fãs de alto desempenho | 1 |
| 865408-B21 | HPE 500-W FS Plat Hot Plug LH Power Supply Kit | 2 |
| 512485-B21 | HPE iLO Adv 1-Server Licença 1 Ano Suporte | 1 |
| 874543-B21 | HPE 1U Gen10 SFF Fácil instalar kit ferroviário | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Implantação da empresa: HPE ProLiant DL20

| Componente | Especificações técnicas |
|--|--|
| Chassis | Servidor de rack 1U |
| Dimensões (altura x largura x profundidade) | 4,32 x 43,46 x 38,22 cm/ 1,70 x 17,11 x 15,05 polegadas |
| Peso | 7,9 kg/17,41 lb |
| Processador | Intel Xeon E-2234, 3.6 GHz, 4C/8T, 71 W |
| Chipset | Intel C242 |
| Memória | 2 x 16-GB Dual Rank x8 DDR4-2666 |
| Armazenamento | 3 x 1-TB SATA 6G Midline 7.2 K SFF (2.5 in) – RAID 5 com Smart Array P408i-a SR Controller |
| Controlador de rede | Bordo: 2 x 1 Gb <br>Bordo: Cartão de porto da OIT 1 Gb <br>Externo: 1 x HPE Ethernet 1-Gb 4-porta Adaptador 366FLR |
| Gestão | HPE iLO Avançado |
| Acesso ao dispositivo | Frente: 1 x USB 3.0, 1 x porta de serviço USB iLO <br>Traseira: 2 x USB 3.0 <br>Interno: 1 x USB 3.0 |
| Energia | Fontes duplas de alimentação de plug-in 500 W |
| Suporte de cremalheira | Kit de trilho de fricção curta HPE 1U |

### <a name="appliance-bom"></a>Aparelho BOM

| PN | Descrição: high end | Quantidade |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO Kit | 1 |
| 879507-B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND Kit | 2 |
| 655710-B21 | HPE 1-TB SATA 7.2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser Kit | 1 |
| 665240-B21 | Adaptador HPE Ethernet 1-Gb 4-porta 366FLR | 1 |
| 782961-B21 | Bateria de armazenamento inteligente HPE 12-W | 1 |
| 869081-B21 | Controlador HPE Smart Array P408i-a SR G10 LH | 1 |
| 865408-B21 | HPE 500-W FS Plat Hot Plug LH Power Supply Kit | 2 |
| 512485-B21 | HPE iLO Adv 1-Server Licença 1 Ano Suporte | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS Enablement FIO Kit | 1 |
| 775612-B21 | Kit de trilho de fricção curta HPE 1U | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>Implantação SMB: HPE ProLiant DL20

| Componente | Especificações técnicas |
|--|--|
| Chassis | Servidor de rack 1U |
| Dimensões (altura x largura x profundidade) | 4,32 x 43,46 x 38,22 cm/ 1,70 x 17,11 x 15,05 polegadas |
| Peso | 7,88 kg/17,37 lb |
| Processador | Intel Xeon E-2224, 3.4 GHz, 4C, 71 W |
| Chipset | Intel C242 |
| Memória | 1 x 8-GB Dual Rank x8 DDR4-2666 |
| Armazenamento | 2 x 1-TB SATA 6G Midline 7.2 K SFF (2.5 in) – RAID 1 com Smart Array P208i-a |
| Controlador de rede | Bordo: 2 x 1 Gb <br>Bordo: Cartão de porto da OIT 1 Gb <br>Externo: 1 x HPE Ethernet 1-Gb 4-porta Adaptador 366FLR |
| Gestão | HPE iLO Avançado |
| Acesso ao dispositivo | Frente: 1 x USB 3.0, 1 x porta de serviço USB iLO <br>Traseira: 2 x USB 3.0 <br>Interno: 1 x USB 3.0 |
| Energia | Alimentação hot plug 290 W |
| Suporte de cremalheira | Kit de trilho de fricção curta HPE 1U |

### <a name="appliance-bom"></a>Aparelho BOM

| PN | Description | Quantidade |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO Kit | 1 |
| 879505-B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND Kit | 1 |
| 801882-B21 | HPE 1-TB SATA 7.2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser Kit | 1 |
| 665240-B21 | Adaptador HPE Ethernet 1-Gb 4-porta 366FLR | 1 |
| 869079-B21 | HPE Smart Array E208i-a SR G10 LH Controlador | 1 |
| P21649-B21 | HPE DL20 Gen10 Plat 290 W FIO PSU Kit | 1 |
| P06683-B21 | Kit de cabo HPE DL20 Gen10 M.2 SATA/LFF AROC | 1 |
| 512485-B21 | HPE iLO Adv 1-Server Licença 1 Ano Suporte | 1 |
| 775612-B21 | Kit de trilho de fricção curta HPE 1U | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB Rugged: HPE Edgeline EL300

| Componente | Especificações técnicas |
|--|--|
| Construção | Design à prova de pó de alumínio, & sem ventilador |
| Dimensões (altura x largura x profundidade) | 200,5 mm (7,9") de altura, 232mm (9,14") de largura por 100mm (3,9") de profundidade |
| Peso | 4,91 KG (10,83 lbs.) |
| CPU | Intel Core i7-8650U (1.9GHz/4-core/15W) |
| Chipset | Intel® Hub de Controlador de Plataforma Q170 |
| Memória | 8GB DDR4 2133MHz Temperatura ampla SODIMM |
| Armazenamento | 128GB 3ME3 Temperatura larga mSATA SSD |
| Controlador de rede | Portas 6x Gigabit Ethernet by Intel® I219 |
| Acesso ao dispositivo  | 4 USBs: 2 frentes; 2 traseiras; 1 interno |
| Adaptador de potência | 250V/10A |
| Montagem | Kit de montagem, Din Rail |
| Temperatura operacional | 0C a +70C  |
| Humidade | 10%~~90%, não condensação |
| Vibração | 0,3 grms 10Hz a 300Hz, 15 minutos por eixo - Carril Din   |
| Choque | 10G 10ms, meio-sena, três para cada eixo. (Ambos positivos & pulso negativo) – Din Rail |

### <a name="appliance-bom"></a>Aparelho BOM
| Produto | Description |
|--|--|
| P25828-B21 | HPE Edgeline EL300 v2 Sistema de Borda Convergida |
| P25828-B21 B19 | HPE EL300 v2 Sistema de Borda Convergida |
| P25833-B21 | Intel Core i7-8650U (1.9GHz/4-core/15W) Kit de processador básico FIO para HPE Edgeline EL300 |
| P09176-B21 | HPE Edgeline 8GB (1x8GB) Dual Rank x8 DDR4-2666 SODIMM WT CAS-19-19-19 Cartão de Memória Registada FIO |
| P09188-B21 | HPE Edgeline 256GB SATA 6G Ler Intensivo M.2 2242 3yr Wty Wide Temp SSD |
| P04054-B21 | HPE Edgeline EL300 SFF para M.2 Kit de Ativação |
| P08120-B21 | HPE Edgeline EL300 12VDC FIO Transfer Board |
| P08641-B21 | HPE Edgeline EL300 80W 12VDC Fonte de alimentação |
| AF564A | HPE C13 - SI-32 IL 250V 10Amp 1.83m Cabo de alimentação |
| P25835-B21 | HPE EL300 v2 FIO Carrier Board |
| R1P49AAE | HPE EL300 iSM Adv 3yr 24x7 Sup_Upd E-LTU |
| P08018-B21 opcional | HPE Edgeline EL300 Kit de suporte de baixo perfil  |
| P08019-B21 opcional | HPE Edgeline EL300 DIN Rail Mount Kit |
| P08020-B21 opcional | Kit de montagem de parede HPE Edgeline EL300 |
| P03456-B21 opcional | HPE Edgeline 1GbE 4-porta TSN FIO Cartão filha |

## <a name="virtual-appliance-specifications"></a>Especificações do aparelho virtual

### <a name="sensors"></a>Sensores

| Tipo | Empresarial | Grandes Empresas | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Memória | 32 GB | 32 GB | 8 GB |
| Armazenamento | 5.6 TB | 1.8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Aparelho de consola de gestão no local

| Tipo | Grandes Empresas |
|--|--|
| Description | Aparelho virtual para tipos de implementação de empresas |
| vCPU | 8 |
| Memória | 32 GB |
| Armazenamento | 1.8 TB |

Hipervisores suportados: VMware ESXi versão 5.0 e mais tarde, Hyper-V

## <a name="additional-certified-appliances"></a>Aparelhos certificados adicionais

Esta secção detalha aparelhos adicionais que foram certificados pela Microsoft mas que não são oferecidos como aparelhos pré-configurados.

| Tipo de implantação | Grandes Empresas |
|--|--|
| Imagem | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Tipo de implementação da empresa."::: |
| Modelação | Dell PowerEdge R340 XL |
| Portas de monitorização | Até nove RJ45 ou seis OPT |
| Largura de banda Max [1](#anchortext2)| 1 Gb/seg |
| Dispositivos max protegidos | 10,000 |

<a id="anchortext2">1.</a> A capacidade de largura de banda pode variar, dependendo da distribuição de protocolos.

Depois de adquirir o aparelho, aceda ao **Defender para** obter  >  **sensores de rede IoT ISO**  >  **Instalação** para descarregar o software.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Sensores de rede ISO.":::

## <a name="next-steps"></a>Passos seguintes

[Sobre o Azure Defender para a instalação IoT](how-to-install-software.md)

[Sobre o Azure Defender para a configuração da rede IoT](how-to-set-up-your-network.md)

