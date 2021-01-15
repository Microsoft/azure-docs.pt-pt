---
title: Teste de produção da rede Azure VM
titlesuffix: Azure Virtual Network
description: Utilize o NTTTCP para direcionar a rede para testes e minimizar a utilização de outros recursos que possam ter impacto no desempenho.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: 7a2f6750a4d0a48c6971f60241976fb55410b65c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221447"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Testes de largura de banda/produção (NTTTCP)

Ao testar o desempenho da produção da rede em Azure, o melhor é usar uma ferramenta que direcione a rede para testes e minimize o uso de outros recursos que possam ter impacto no desempenho. Recomenda-se o NTTTCP.

Copie a ferramenta para dois VMs Azure do mesmo tamanho. Um VM funciona como SENDER e o outro como RECETOR.

#### <a name="deploying-vms-for-testing"></a>Implantação de VMs para testes
Para efeitos deste teste, os dois VMs devem estar no mesmo Grupo de [Colocação](../virtual-machines/co-location.md) de Proximidade ou no mesmo Conjunto de Disponibilidade para que possamos utilizar os seus IPs internos e excluir os Balançadores de Carga do teste. É possível testar com o VIP, mas este tipo de testes está fora do âmbito deste documento.

Tome nota do endereço IP do RECETOR. Vamos chamar o IP de "a.b.c.r"

Tome nota do número de núcleos no VM. Vamos chamar isto de \# \_ "núcleos num".

Escote o teste NTTTCP durante 300 segundos (ou 5 minutos) no VM e no recetor VM.

Sugestão: Ao configurar este teste pela primeira vez, poderá tentar um período de teste mais curto para obter feedback mais cedo. Uma vez que a ferramenta funcione como esperado, estenda o período de teste para 300 segundos para obter os resultados mais precisos.

> [!NOTE]
> O remetente **e** **o** recetor devem especificar o mesmo parâmetro de duração do teste (-t).

Para testar uma única corrente de TCP durante 10 segundos:

Parâmetros do recetor: ntttcp -r -t 10 -P 1

Parâmetros remetentes: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> A amostra anterior só deve ser utilizada para confirmar a sua configuração. Exemplos válidos de testes são cobertos mais tarde neste documento.

## <a name="testing-vms-running-windows"></a>Teste de VMs em execução WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Coloca o NTTTCP nos VMs.

Descarregue a versão mais recente: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Ou procure-o se for movido: <https://www.bing.com/search?q=ntttcp+download> \< - deve ser o primeiro sucesso

Considere colocar o NTTTCP numa pasta separada, como c: \\ ferramentas

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Permitir o NTTTCP através da firewall do Windows
No RECETOR, crie uma regra de permitir a chegada do tráfego NTTTCP. É mais fácil permitir todo o programa NTTTCP pelo nome em vez de permitir a entrada de portas TCP específicas.

Permitir que o NTTTCP passe pela Firewall do Windows desta forma:

netsh advfirewall firewall adicionar programa de regra= \<PATH\> \\ntttcp.exe nome="ntttcp" protocolo=any dir=in action=allow enable=yes profile=ANY

Por exemplo, se copiasse ntttcp.exe para a pasta "c: \\ ferramentas", este seria o comando: 

netsh advfirewall firewall adicionar programa de regra=c: \\ ferramentas \\ntttcp.exe protocolo de nome="ntttcp" =qualquer dir=em ação=permitir ativar=sim perfil=ANY

#### <a name="running-ntttcp-tests"></a>Realização de testes NTTTCP

Iniciar o NTTTCP no recetor **(executado a partir de CMD**, não de PowerShell):

ntttcp -r -m [2 \* \# \_ núcleos num], \* a.b.c.r -t 300

Se o VM tiver quatro núcleos e um endereço IP de 10.0.0.4, seria assim:

ntttcp -r -m 8, \* ,10.0.0.4 -t 300


Iniciar ntTTCP no SENDER **(executado a partir de CMD**, não de PowerShell):

NTTTCP -s -m 8, \* 10.0.0.4 -t 300 

Espere pelos resultados.


## <a name="testing-vms-running-linux"></a>Teste de VMs em execução LINUX:

Use nttcp-for-linux. Está disponível a partir de <https://github.com/Microsoft/ntttcp-for-linux>

Nos VMs Linux (tanto SENDER como RECETOR), execute estes comandos para preparar o ntttcp-for-linux nos seus VMs:

CentOS - Instalar Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Instalar Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Fazer e instalar em ambos:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Como no exemplo do Windows, assumimos que o IP do recetor Linux é de 10.0.0.4

Iniciar o NTTTCP-for-Linux no RECETOR:

``` bash
ntttcp -r -t 300
```

E no REMETENTE, corra:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
O comprimento do teste não pode ser de 60 segundos se não for dado nenhum parâmetro de tempo

## <a name="testing-between-vms-running-windows-and-linux"></a>Testes entre VMs em execução Windows e LINUX:

Nestes cenários, devemos ativar o modo de não sincronização para que o teste possa ser executado. Isto é feito usando a **bandeira -N** para Linux, e **-ns bandeira** para Windows.

#### <a name="from-linux-to-windows"></a>De Linux a Windows:

\<Windows>Recetor:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

\<Linux>Remetente:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Das janelas a Linux:

\<Linux>Recetor:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

\<Windows>Remetente:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testar instâncias de serviço em nuvem:
Tem de adicionar a seguinte secção no seu ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Passos seguintes
* Dependendo dos resultados, pode haver espaço para [otimizar as máquinas de produção de rede](virtual-network-optimize-network-bandwidth.md) para o seu cenário.
* Leia sobre como [a largura de banda é atribuída a máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com [a Azure Virtual Network perguntas frequentes (FAQ)](virtual-networks-faq.md)