---
title: Teste de débito de rede de VM do Azure
titlesuffix: Azure Virtual Network
description: Saiba como testar o débito de rede de máquina virtual do Azure.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 80e8a5e5de1da2098d895e09b36fb209050743a0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481405"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Largura de banda/débito testes (NTTTCP)

Ao testar o desempenho de taxa de transferência de rede no Azure, é melhor usar uma ferramenta destina-se a rede para fins de teste e minimiza o uso de outros recursos que podem afetar o desempenho. Recomenda-se NTTTCP.

Copie a ferramenta para duas VMs do Azure do mesmo tamanho. Uma VM funciona como remetente e o outro como receptor.

#### <a name="deploying-vms-for-testing"></a>Implementação de VMs para teste
Para efeitos deste teste, as duas VMs devem ser no mesmo serviço Cloud ou no mesmo conjunto de disponibilidade, de modo que podemos usar dos respetivos IPs interno e excluir os balanceadores de carga do teste. É possível testar com o VIP, mas esse tipo de teste está fora do escopo deste documento.

Tome nota do endereço IP do receptor. Vamos chamar esse IP "a.b.c.r"

Anote o número de núcleos na VM. Vamos chamá-lo "\#núm\_núcleos"

Execute o teste NTTTCP para 300 segundos (ou 5 minutos) na VM do remetente e receptor de VM.

Sugestão: Ao configurar este teste pela primeira vez, poderia tentar um período de teste mais curto para obter comentários mais cedo. Quando a ferramenta está a funcionar conforme esperado, alargar o período de teste a 300 segundos para os resultados mais precisos.

> [!NOTE]
> O remetente **e** recetor tem de especificar **o mesmo** testar o parâmetro de duração (-t).

Para testar um único fluxo TCP para 10 segundos:

Parâmetros do receptor: ntttcp - r -t 10 - P 1

Parâmetros de remetente: ntttcp-s10.27.33.7 -t 10 - n 1 -P 1

> [!NOTE]
> O exemplo anterior só deve ser utilizado para confirmar a sua configuração. Exemplos válidos de teste serão abordados mais adiante neste documento.

## <a name="testing-vms-running-windows"></a>Teste de VMs que executam o WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Obtenha NTTTCP para as VMs.

Baixe a versão mais recente: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Ou procure-se movido: <https://www.bing.com/search?q=ntttcp+download> \< – deve ser atingido pela primeira vez

Considere colocar NTTTCP na pasta separada, como c:\\ferramentas

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Permitir NTTTCP através da firewall do Windows
No receptor, crie uma regra de permissão na Firewall do Windows para permitir o tráfego NTTTCP a chegada. É mais fácil permitir que o programa NTTTCP inteiro por nome em vez de permitir que portas específicas do TCP de entrada.

Permitir ntttcp através da Firewall do Windows como este:

netsh advfirewall firewall adicionar programa da regra =\<caminho\>\\ntttcp.exe nome = "ntttcp" protocolo = qualquer dir = em ação = permitir enable = yes profile = ANY

Por exemplo, se copiou ntttcp.exe para o "c:\\ferramentas" pasta, isso seria o comando: 

netsh advfirewall firewall adicionar programa da regra = c:\\ferramentas\\ntttcp.exe nome = "ntttcp" protocolo = qualquer dir = em ação = permitir enable = yes profile = ANY

#### <a name="running-ntttcp-tests"></a>Testes NTTTCP em execução

Iniciar NTTTCP no receptor (**execute a partir de CMD**, não a partir do PowerShell):

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

Se a VM tem quatro núcleos e um endereço IP de 10.0.0.4, ficaria assim:

ntttcp -r –m 8,\*,10.0.0.4 -t 300


Iniciar NTTTCP no remetente (**execute a partir de CMD**, não a partir do PowerShell):

ntttcp -s –m 8,\*,10.0.0.4 -t 300 

Aguarde que os resultados.


## <a name="testing-vms-running-linux"></a>Teste de VMs em execução no LINUX:

Utilizar o nttcp para linux. Está disponível a partir de <https://github.com/Microsoft/ntttcp-for-linux>

Em VMs do Linux (remetente e destinatário), execute estes comandos para preparar ntttcp-para-linux nas suas VMs:

CentOS - Install Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Install Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Faça e instalar em ambos:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Como no exemplo do Windows, partimos do princípio de que IP do RECETOR de Linux está 10.0.0.4

Inicie NTTTCP-para-Linux no receptor:

``` bash
ntttcp -r -t 300
```

E no remetente, execute:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Recebe o teste comprimento assume a predefinição de 60 segundos, se nenhum parâmetro de tempo

## <a name="testing-between-vms-running-windows-and-linux"></a>Teste de entre as VMs que executam o Windows e LINUX:

Sobre este cenários devemos habilitar o modo sem sincronização para que pode executar o teste. Isso é feito utilizando o **sinalizador -N** para o Linux, e **sinalizador -ns** para Windows.

#### <a name="from-linux-to-windows"></a>Do Linux ao Windows:

Receiver \<Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Remetente \<Linux >:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Do Windows para Linux:

Recetor \<Linux >:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Remetente \<Windows >:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Teste as instâncias de serviço Cloud:
Precisa adicionar o seguinte secção em sua servicedefinition. Csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Passos Seguintes
* Consoante os resultados, pode haver espaço para [otimizar máquinas de débito de rede](virtual-network-optimize-network-bandwidth.md) para o seu cenário.
* Saiba mais sobre como [largura de banda atribuída às máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com [rede Virtual do Azure, perguntas freqüentes (FAQ sobre)](virtual-networks-faq.md)
