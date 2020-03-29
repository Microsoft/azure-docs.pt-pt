---
title: Testar a entrada da rede Azure VM
titlesuffix: Azure Virtual Network
description: Aprenda a testar a entrada da rede de máquinas virtuais Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60743087"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Teste de largura de banda/de supor (NTTTCP)

Ao testar o desempenho da rede de suporte de suporte em Azure, o melhor é utilizar uma ferramenta que direciona a rede para testes e minimiza o uso de outros recursos que possam ter impacto no desempenho. É recomendado o NTTTCP.

Copie a ferramenta para dois VMs Azure do mesmo tamanho. Um VM funciona como SENDER e o outro como RECETOR.

#### <a name="deploying-vms-for-testing"></a>Implementação de VMs para testes
Para efeitos deste teste, os dois VMs devem estar no mesmo Serviço de Cloud ou no mesmo Conjunto de Disponibilidade para que possamos usar os seus IPs internos e excluir os Balancers de Carga do teste. É possível testar com o VIP, mas este tipo de testes está fora do âmbito deste documento.

Tome nota do endereço IP do RECETOR. Vamos chamar a isso IP "a.b.c.r"

Tome nota do número de núcleos no VM. Vamos chamar isto\#de\_"núcleos num".

Faça o teste NTTTCP durante 300 segundos (ou 5 minutos) no vm do remetente e no recetor VM.

Dica: Ao configurar este teste pela primeira vez, poderá tentar um período de teste mais curto para obter feedback mais cedo. Uma vez que a ferramenta funcione como esperado, prolongue o período de teste para 300 segundos para obter os resultados mais precisos.

> [!NOTE]
> O remetente **e** **o** recetor devem especificar o mesmo parâmetro de duração do ensaio (t).

Para testar um único fluxo de TCP durante 10 segundos:

Parâmetros recetores: ntttcp -r -t 10 -P 1

Parâmetros do remetente: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> A amostra anterior só deve ser utilizada para confirmar a sua configuração. Exemplos válidos de testes são cobertos mais tarde neste documento.

## <a name="testing-vms-running-windows"></a>Teste vMs executando WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Mande a NTTTCP para os VMs.

Faça o download da versão mais recente:<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Ou procurá-lo se <https://www.bing.com/search?q=ntttcp+download> \< movido: - deve ser o primeiro atingido

Considere colocar a NTTTCP em\\pasta separada, como c: ferramentas

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Permitir ntTTCP através da firewall do Windows
No RECETOR, crie uma regra de permitir a firewall do Windows para permitir a chegada do tráfego NTTTCP. É mais fácil permitir que todo o programa NTTTCP seja o nome, em vez de permitir a entrada de portas TCP específicas.

Permita o ntttcp através do Firewall do Windows desta forma:

netsh advfirewall firewall adicionar\<\>\\programa de regras= PATH nttcp.exe name="ntttcp" protocolo=qualquer dir=em ação=permitir=sim perfil=ANY

Por exemplo, se copiasse ntttcp.exe\\para a pasta "c: ferramentas", este seria o comando: 

netsh firewall firewall adicionar programa de\\\\regra=c: ferramentas nttcp.exe name="ntttcp" protocolo=qualquer dir=em ação=permitir=permitir=sim perfil=ANY

#### <a name="running-ntttcp-tests"></a>Testes NTTTCP em execução

Iniciar ntTTCP no RECETOR **(executado a partir de CMD,** não da PowerShell):

ntttcp -r –m\*\#\_[2\*núcleos num], a.b.c.r -t 300

Se o VM tiver quatro núcleos e um endereço IP de 10.0.0.4, seria assim:

ntttcp -r -\*8, ,10.0.0.4 -t 300


Iniciar ntTTCP no SENDER **(executado a partir de CMD,** não da PowerShell):

ntttcp -s -\*8, ,10.0.0.4 -t 300 

Aguarde os resultados.


## <a name="testing-vms-running-linux"></a>VMs de teste que executam o LINUX:

Utilize nttcp-for-linux. Está disponível a partir de<https://github.com/Microsoft/ntttcp-for-linux>

Nos VMs Linux (sENDER e RECETOR), execute estes comandos para preparar nttcp-for-linux nos seus VMs:

CentOS - Instale git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Instale Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Faça e instale em ambos:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Tal como no exemplo do Windows, assumimos que o IP do recetor linux é 10.0.0.4

Inicie ntTTCP-for-Linux no RECETOR:

``` bash
ntttcp -r -t 300
```

E no SENDER, corra:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
O comprimento do teste falha em 60 segundos se não for dado um parâmetro de tempo

## <a name="testing-between-vms-running-windows-and-linux"></a>Teste entre VMs executando Windows e LINUX:

Nestes cenários, devemos ativar o modo sem sincronização para que o teste possa ser executado. Isto é feito usando a **bandeira -N** para Linux, e **-ns bandeira** para Windows.

#### <a name="from-linux-to-windows"></a>De Linux a Windows:

O \<recetor Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Sender \<Linux>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Das janelas ao Linux:

Recetor \<Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Janelas \<do remetente>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testar as instâncias de serviço em nuvem:
Tem de adicionar a seguinte secção ao seu ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Passos seguintes
* Dependendo dos resultados, pode haver espaço para [otimizar as máquinas](virtual-network-optimize-network-bandwidth.md) de entrada de rede para o seu cenário.
* Leia sobre como a [largura de banda é atribuída a máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com a [Rede Virtual Azure frequentemente colocada seleções (FAQ)](virtual-networks-faq.md)
