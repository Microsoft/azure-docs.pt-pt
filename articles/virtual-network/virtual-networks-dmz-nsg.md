---
title: Exemplo de rede de Perímetro do Azure – criar um DMZ Simple com NSGs | Documentos da Microsoft
description: Criar um DMZ com grupos de segurança de rede (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 68655ea03f53fe7100f67d111fcd3c8595bdf4c9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109397"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Exemplo 1 – criar um DMZ simple com NSGs com um modelo Azure Resource Manager
[Regressar à página de práticas recomendada de segurança limites][HOME]

> [!div class="op_single_selector"]
> * [Modelo do Resource Manager](virtual-networks-dmz-nsg.md)
> * [Clássica – PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Este exemplo cria uma rede de Perímetro primitivo com quatro servidores do Windows e os grupos de segurança de rede. Este exemplo descreve cada uma das secções relevantes do modelo para fornecer uma compreensão mais aprofundada de cada passo. Também há uma seção de cenário de tráfego para fornecer uma visão mais detalhada passo a passo como o tráfego passa pelas camadas de defesa na rede de Perímetro. Finalmente, as referências de seção é o código de modelo completo e as instruções para criar este ambiente de teste e experimente com vários cenários. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Rede de Perímetro com NSG de entrada][1]

## <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, uma subscrição contém os seguintes recursos:

* Um grupo de recursos
* Uma rede Virtual com duas sub-redes; "Front-end" e "Back-end"
* Um grupo de segurança de rede que é aplicado a ambas as sub-redes
* Windows Server que representa um servidor de web de aplicações ("IIS01")
* Servidores de duas janelas que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")
* Um endereço IP público associado ao servidor de web de aplicação

Na seção referências, há um link para um modelo do Azure Resource Manager que cria o ambiente descrito neste exemplo. A criar as VMs e redes virtuais, embora feito pelo modelo de exemplo, não estão descritas em detalhe neste documento. 

**Para criar este ambiente** (instruções detalhadas estão na seção referências deste documento);

1. Implemente o modelo Azure Resource Manager em: [Modelos de início rápido do Azure][Template]
2. Instale a aplicação de exemplo em: [Script de aplicação de exemplo][SampleApp]

>[!NOTE]
>Para estabelecer o RDP para quaisquer servidores de back-end nesta instância, o servidor IIS é utilizado como um "jumpbox." Primeira RDP para o servidor IIS e, em seguida, para o RDP do servidor de IIS para o servidor de back-end. Em alternativa um IP público pode ser associado a cada NIC para RDP mais fácil de servidor.
> 
>

As secções seguintes fornecem uma descrição detalhada do grupo de segurança de rede e como ele funciona para este exemplo, acompanhando as linhas chave do modelo do Azure Resource Manager.

## <a name="network-security-groups-nsg"></a>Grupos de Segurança de Rede (NSG)
Neste exemplo, um grupo NSG é criado e, em seguida, é carregado com regras de seis. 

>[!TIP]
>Em termos gerais, deve criar as regras específicas de "Permitir de" primeiro e, em seguida, as regras mais genéricas de "Negar" último. Define a prioridade atribuída que as regras são avaliadas primeiro. Assim que o tráfego for encontrado para aplicar a uma regra específica, não existem regras adicionais são avaliadas. As regras do NSG podem aplicar em qualquer um na direção de entrada ou saída (a partir do ponto de vista da sub-rede).
>
>

De forma declarativa, as seguintes regras estão sendo criadas para tráfego de entrada:

1. É permitido o tráfego DNS interno (porta 53)
2. É permitido o tráfego RDP (porta 3389) da Internet a qualquer VM
3. O tráfego HTTP (porta 80) da Internet para o servidor web (IIS01) é permitido
4. Qualquer tráfego (todas as portas) de IIS01 para AppVM1 é permitido
5. Qualquer tráfego (todas as portas) da Internet para toda a VNet (ambas as sub-redes) é negado
6. Qualquer tráfego (todas as portas) partir da sub-rede Frontend para a sub-rede de back-end é negado

Com estas regras vinculado a cada sub-rede, se um pedido de HTTP foi entrado da Internet para o servidor web, ambas as regras 3 (permitir) e 5 (negar) aplicaria, mas uma vez que a regra 3 tem uma prioridade mais alta apenas seriam aplicáveis e regra 5 não seria entram em cena. Assim, a solicitação HTTP teria permissão para o servidor web. Se esse mesmo tráfego estava a tentar aceder ao servidor DNS01, regra 5 (negar) seria o primeiro a aplicar e o tráfego que não permitia a passar para o servidor. Regra 6 (negar) bloqueia a sub-rede de front-end ao falar com a sub-rede de back-end (exceto para o tráfego permitido nas regras 1 e 4), este conjunto de regras protege a rede de back-end, no caso de comprometimentos um atacante a aplicação web no front-end, o invasor teria limitada acesso para o back-end "protegido" rede (apenas para recursos expostos no servidor AppVM01).

Existe uma regra de saída predefinida que permita o tráfego de saída à internet. Neste exemplo, estamos a permitir o tráfego de saída e sem modificar quaisquer regras de saída. Para aplicar a política de segurança para o tráfego em ambas as direções, encaminhamento de definido pelo utilizador é necessário e é explorada "Exemplo 3" no [página de práticas recomendadas do Security limites][HOME].

Cada regra é abordada em mais detalhes, da seguinte forma:

1. Um recurso do grupo de segurança de rede tem de ser instanciado para conter as regras:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. Neste exemplo, a primeira regra permite o tráfego DNS entre todas as redes internas para o servidor DNS na sub-rede de back-end. A regra tem alguns parâmetros importantes:
   * "destinationAddressPrefix" - o prefixo de endereço de destino está definido como "10.0.2.4", para que o tráfego DNS tem permissão para aceder ao servidor de DNS.
   * "Direção" significa que em que direção do fluxo de tráfego entra em vigor a esta regra. É a direção da perspetiva da sub-redes ou máquinas virtuais (dependendo de onde está vinculada este NSG). Portanto, se direção é "Entrada" e tráfego está a entrar na sub-rede, a regra aplicaria e tráfego que sai da sub-rede não seria afetado por esta regra.
   * "Prioridade" define a ordem em que um fluxo de tráfego é avaliado. Menor o número maior será a prioridade. Quando uma regra se aplica a um fluxo de tráfego específico, não existem mais regras são processadas. Portanto, se uma regra com prioridade 1 permite o tráfego e uma regra com prioridade 2 recusa o tráfego e ambas as regras são aplicadas ao tráfego, em seguida, o tráfego teria permissão para o fluxo (uma vez que a regra 1 tinha uma prioridade mais alta demorou efeito e não mais regras foram aplicadas).
   * "Acesso" significa se o tráfego afetado por esta regra está bloqueado ("Negar") ou permitido ("Permitir").

     ```JSON
     "properties": {
     "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
     ```

3. Esta regra permite o tráfego RDP para o fluxo da internet para a porta RDP em qualquer servidor na sub-rede vinculada. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Esta regra permite o tráfego de internet de entrada para atingir o servidor web. Esta regra não altera o comportamento do encaminhamento. A regra permite apenas tráfego destinado a IIS01 passar. Portanto, se o tráfego da Internet tinha o servidor web como respectivo destino esta regra seria permitir que ele e parar o processamento ainda mais regras. (Na regra com prioridade 140 todos os outro internet tráfego de entrada é bloqueado). Se estiver apenas a processar o tráfego HTTP, esta regra pode ser mais restrito para permitir apenas a porta 80 de destino.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Esta regra permite o tráfego para passar do servidor IIS01 para o servidor de AppVM01, um posterior blocos de regra a todos os outro front-end para back-end o tráfego. Para melhorar esta regra, se a porta é conhecida que devem ser adicionados. Por exemplo, se o servidor IIS está atingindo apenas SQL Server em AppVM01, o intervalo de portas de destino deve ser alterado de "*" (qualquer) 1433 (a porta do SQL), permitindo assim que uma menor superfície de ataque de entrada em AppVM01 a aplicação web já estiver comprometida.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Regras podem usar um tipo especial de prefixo de endereço chamado uma "etiqueta predefinida", essas marcas são identificadores fornecidos pelo sistema que permitem uma forma fácil de abordar uma categoria maior de prefixos de endereço. Esta regra utiliza a etiqueta predefinida "VirtualNetwork" para o prefixo de endereço de destino para indicar qualquer endereço dentro da VNet. Outras etiquetas de prefixo são Internet e AzureLoadBalancer. Esta regra nega o tráfego da internet para todos os servidores na rede. Com as regras de prioridade 110 e 120, o efeito é permitir que apenas internet tráfego de entrada para o firewall e as portas RDP em servidores e blocos de todo o resto. Esta regra é uma regra "isento de falhas" para bloquear todos os fluxos inesperados.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. A regra final recusa o tráfego da sub-rede do front-end para a sub-rede de back-end. Uma vez que esta regra é uma única regra de entrada, o tráfego inverso é permitido (a partir do back-end para o front-end).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-internet-to-web-server"></a>(*Permitidos*) Internet para o servidor web
1. Um utilizador de internet solicita uma página HTTP do endereço IP da NIC associado ao NIC IIS01
2. O endereço IP público passa o tráfego para a VNet para IIS01 (o servidor web)
3. A sub-rede de front-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. 2 de regra de NSG (RDP) não se aplicam, mudar para a próxima regra
   3. Aplicar a regra de NSG 3 (Internet para IIS01), o tráfego é o processamento da regra permitidos, pare
4. O tráfego chega o endereço IP interno do servidor web IIS01 (10.0.1.5N)
5. IIS01 está à escuta de tráfego da web, recebe essa solicitação e começa a processar o pedido
6. IIS01 pede-lhe o SQL Server no AppVM01 informações
7. Não existem regras de saída na sub-rede de front-end, o tráfego é permitido
8. A sub-rede de back-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. 2 de regra de NSG (RDP) não se aplicam, mudar para a próxima regra
   3. 3 de regra de NSG (Internet à Firewall) não se aplicam, mudar para a próxima regra
   4. 4 de regra de NSG (IIS01 para AppVM01) é aplicável, o tráfego é permitido, parar o processamento da regra
9. AppVM01 recebe a consulta SQL e responde
10. Uma vez que não existem regras de saída da sub-rede de back-end, a resposta é permitida
11. A sub-rede de front-end começa o processamento da regra de entrada:
    1. Não existe nenhuma regra NSG aplica-se a entrada tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma do NSG de regras aplicam-se
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido.
12. O servidor IIS recebe a resposta SQL e conclui a resposta HTTP e envia para o autor do pedido
13. Uma vez que não existem regras de saída da sub-rede de front-end, a resposta é permitida e o utilizador de Internet recebe a página da web solicitado.

#### <a name="allowed-rdp-to-iis-server"></a>(*Permitidos*) RDP ao servidor IIS
1. Um administrador do servidor na internet solicita uma sessão do RDP para IIS01 no endereço IP público do NIC associada com a NIC de IIS01 (este endereço IP público pode ser encontrado através do Portal ou PowerShell)
2. O endereço IP público passa o tráfego para a VNet para IIS01 (o servidor web)
3. A sub-rede de front-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. Aplicar a regra de NSG 2 (RDP), o tráfego é o processamento da regra permitidos, pare
4. Com não existem regras de saída, aplicam-se de regras predefinidas e tráfego de retorno é permitido
5. Sessão RDP está ativada
6. IIS01 pede-lhe o nome de utilizador e palavra-passe

>[!NOTE]
>Para estabelecer o RDP para quaisquer servidores de back-end nesta instância, o servidor IIS é utilizado como um "jumpbox." Primeira RDP para o servidor IIS e, em seguida, para o RDP do servidor de IIS para o servidor de back-end.
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Permitidos*) consulta DNS do servidor Web no servidor DNS
1. Web Server, IIS01, necessidades de dados de um feed em www.data.gov, mas tem de resolver o endereço.
2. A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede de back-end) que o servidor DNS primário, IIS01 envia o pedido DNS para DNS01
3. Não existem regras de saída na sub-rede de front-end, o tráfego é permitido
4. A sub-rede de back-end começa o processamento da regra de entrada:
   * Aplicar a regra de NSG 1 (DNS), o tráfego é o processamento da regra permitidos, pare
5. Servidor DNS recebe o pedido
6. Servidor DNS não tem o endereço em cache e pede-lhe um servidor DNS de raiz na internet
7. Não existem regras de saída na sub-rede de back-end, o tráfego é permitido
8. Servidor DNS de Internet responde, uma vez que esta sessão foi iniciada internamente, a resposta é permitida
9. Servidor DNS coloca a resposta em cache e responde à solicitação inicial para IIS01
10. Não existem regras de saída na sub-rede de back-end, o tráfego é permitido
11. A sub-rede de front-end começa o processamento da regra de entrada:
    1. Não existe nenhuma regra NSG aplica-se a entrada tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma do NSG de regras aplicam-se
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido
12. IIS01 recebe a resposta do DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitidos*) ficheiro de acesso do servidor Web no AppVM01
1. IIS01 solicita um ficheiro num AppVM01
2. Não existem regras de saída na sub-rede de front-end, o tráfego é permitido
3. A sub-rede de back-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. 2 de regra de NSG (RDP) não se aplicam, mudar para a próxima regra
   3. Regra de NSG 3 (Internet para IIS01) não se aplicam, mova a seguinte regra
   4. 4 de regra de NSG (IIS01 para AppVM01) é aplicável, o tráfego é permitido, parar o processamento da regra
4. AppVM01 recebe a solicitação e responde com o ficheiro (partindo do princípio de acesso está autorizado)
5. Uma vez que não existem regras de saída da sub-rede de back-end, a resposta é permitida
6. A sub-rede de front-end começa o processamento da regra de entrada:
   1. Não existe nenhuma regra NSG aplica-se a entrada tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma do NSG de regras aplicam-se
   2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido.
7. O servidor IIS recebe o ficheiro.

#### <a name="denied-rdp-to-backend"></a>(*Negado*) RDP para back-end
1. Um utilizador de internet tentar fazer o RDP ao servidor AppVM01
2. Uma vez que não existem nenhum endereço IP público associado a esta NIC de servidores, este tráfego nunca deve introduzir a VNet e não alcançar o servidor
3. No entanto se um endereço IP público foi ativado por algum motivo, a regra NSG 2 (RDP) permitiria que esse tráfego

>[!NOTE]
>Para estabelecer o RDP para quaisquer servidores de back-end nesta instância, o servidor IIS é utilizado como um "jumpbox." Primeira RDP para o servidor IIS e, em seguida, para o RDP do servidor de IIS para o servidor de back-end.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Negado*) Web para o servidor back-end
1. Um utilizador de internet tentar acessar um arquivo no AppVM01
2. Uma vez que não existem nenhum endereço IP público associado a esta NIC de servidores, este tráfego nunca deve introduzir a VNet e não alcançar o servidor
3. Se um endereço IP público foi ativado por algum motivo, a regra NSG 5 (Internet a VNet) bloquearia deste tráfego

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Negado*) consulta de DNS de Web no servidor DNS
1. Um utilizador de internet tenta procurar um registo DNS interno no DNS01
2. Uma vez que não existem nenhum endereço IP público associado a esta NIC de servidores, este tráfego nunca deve introduzir a VNet e não alcançar o servidor
3. Se um endereço IP público foi ativado por algum motivo, a regra NSG 5 (Internet a VNet) bloquearia este tráfego (Nota: que regra 1 (DNS) não seriam aplicadas porque os pedidos de endereço de origem é a internet e a regra 1 aplica-se apenas a VNet local como origem)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Negado*) acesso SQL no servidor web
1. Um utilizador de internet solicita dados SQL IIS01
2. Uma vez que não existem nenhum endereço IP público associado a esta NIC de servidores, este tráfego nunca deve introduzir a VNet e não alcançar o servidor
3. Se um endereço IP público foi ativado por algum motivo, a sub-rede de front-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. 2 de regra de NSG (RDP) não se aplicam, mudar para a próxima regra
   3. Aplicar a regra de NSG 3 (Internet para IIS01), o tráfego é o processamento da regra permitidos, pare
4. O tráfego chega o endereço IP interno do IIS01 (10.0.1.5N)
5. IIS01 não está a escutar na porta 1433, então, não há resposta ao pedido

## <a name="conclusion"></a>Conclusão
Neste exemplo é uma maneira relativamente simples e direta de isolar a sub-rede de back-end do tráfego de entrada.

Mais exemplos e uma descrição geral dos limites de segurança de rede podem ser encontradas [aqui][HOME].

## <a name="references"></a>Referências
### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Este exemplo utiliza um modelo predefinido do Azure Resource Manager num repositório GitHub mantido pela Microsoft e abrir para a Comunidade. Este modelo pode ser implementado diretamente do GitHub ou transferido e modificado para atender às suas necessidades. 

O modelo principal é no ficheiro denominado "azuredeploy. JSON". Este modelo pode ser submetido através do PowerShell ou CLI (com o ficheiro associado "azuredeploy") para implementar este modelo. Acho que a forma mais fácil é usar o botão "Implementar no Azure" na página README.md no GitHub.

Para implementar o modelo que cria este exemplo a partir do GitHub e o portal do Azure, siga estes passos:

1. Num browser, navegue para o [modelo][Template]
2. Clique no botão "Implementar no Azure" (ou no botão de "Visualizar" para ver uma representação gráfica deste modelo)
3. Introduza a conta de armazenamento, nome de utilizador e palavra-passe no painel de parâmetros, em seguida, clique em **OK**
5. Criar um grupo de recursos para esta implementação (pode utilizar um já existente, mas recomendo um novo para obter melhores resultados)
6. Se necessário, altere as definições de subscrição e localização para a sua VNet.
7. Clique em **consultar termos legais**, leia os termos e clique em **Compra** entrar em acordo.
8. Clique em **criar** para iniciar a implementação deste modelo.
9. Depois de concluída a implementação com êxito, navegue para o grupo de recursos que criou para esta implementação para ver os recursos configurados no interior.

>[!NOTE]
>Este modelo permite que o RDP para apenas o servidor de IIS01 (localizar o IP público para IIS01 no Portal). Para estabelecer o RDP para quaisquer servidores de back-end nesta instância, o servidor IIS é utilizado como um "jumpbox." Primeira RDP para o servidor IIS e, em seguida, para o RDP do servidor de IIS para o servidor de back-end.
>
>

Para remover esta implementação, elimine o grupo de recursos e todos os recursos subordinados também serão eliminados.

#### <a name="sample-application-scripts"></a>Scripts de aplicativo de exemplo
Assim que o modelo é executado com êxito, pode configurar o servidor web e o servidor de aplicações com uma aplicação web simples para permitir que o teste com esta configuração de rede de Perímetro. Para instalar um aplicativo de exemplo para esse e outros exemplos de rede de Perímetro, foi fornecido na seguinte hiperligação: [Script de aplicação de exemplo][SampleApp]

## <a name="next-steps"></a>Passos Seguintes

* Implementar neste exemplo
* Criar a aplicação de exemplo
* Testar os fluxos de tráfego diferentes através desta rede de Perímetro

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Rede de Perímetro com NSG de entrada"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md
