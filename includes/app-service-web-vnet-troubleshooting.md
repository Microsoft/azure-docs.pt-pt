---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419558"
---
A funcionalidade é fácil de configurar, mas isso não significa que a sua experiência será livre de problemas. Se encontrar problemas de acesso ao ponto final pretendido, existem alguns utilitários que pode utilizar para testar a conectividade a partir da consola de aplicações. Há duas consolas que podes usar. Uma é a consola Kudu, e a outra é a consola no portal Azure. Para chegar à consola Kudu a partir da sua aplicação, vá a **Tools** > **Kudu**. Você também pode chegar à consola Kudo em [sitename].scm.azurewebsites.net. Depois de o site carregar, vá ao separador **de consola Debug.** Para chegar à consola azure hospedada pelo portal a partir da sua aplicação, vá para **tools** > **console**.

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup**, e **tracert** não funcionarão através da consola devido a restrições de segurança. Para preencher o vazio, são adicionadas duas ferramentas separadas. Para testar a funcionalidade DNS, adicionámos uma ferramenta chamada **nameresolver.exe**. A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Pode utilizar nameresolver para verificar os nomes de anfitriões de que a sua aplicação depende. Desta forma, pode testar se tem algo mal configurado com o seu DNS ou talvez não tenha acesso ao seu servidor DNS. Pode ver o servidor DNS que a sua aplicação utiliza na consola olhando para as variáveis ambientais WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

Pode utilizar a ferramenta seguinte para testar a conectividade TCP com uma combinação de hospedeiro e porta. Esta ferramenta chama-se **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário **tcpping** diz-lhe se pode chegar a um hospedeiro específico e porto. Só pode mostrar sucesso se houver uma aplicação a ouvir a combinação de anfitriões e portas, e há acesso de rede da sua app ao anfitrião especificado e à porta.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Depuração de acesso a recursos de rede virtuais hospedados em rede
Várias coisas podem impedir que a sua aplicação chegue a um anfitrião específico e à porta. A maior parte do tempo é uma destas coisas:

* **Uma firewall está no caminho.** Se tiver uma firewall no caminho, atinja o intervalo do TCP. O tempo de saída do TCP é de 21 segundos neste caso. Utilize a ferramenta **de tcpping** para testar a conectividade. Os intervalos de tCP podem ser causados por muitas coisas além das firewalls, mas comecem por aí.
* **DNS não é acessível.** O tempo de saída do DNS é de 3 segundos por servidor DNS. Se tiver dois servidores DNS, o tempo é de 6 segundos. Use nameresolver para ver se o DNS está funcionando. Não pode usar o nslookup, porque isso não usa o DNS com o qual a sua rede virtual está configurada. Se não for acessível, pode ter uma firewall ou NSG bloqueando o acesso ao DNS ou pode estar em baixo.

Se esses itens não responderem aos seus problemas, procure primeiro coisas como:

**Integração Regional de VNet**
* O seu destino é um endereço não RFC1918 e não tem WEBSITE_VNET_ROUTE_ALL definido para 1?
* Existe algum nSG bloqueando a saída da sua subnet de integração?
* Se estiver a atravessar o Azure ExpressRoute ou uma VPN, o seu portal no local está configurado para encaminhar o tráfego de volta para Azure? Se conseguir chegar a pontos finais na sua rede virtual, mas não no local, verifique as suas rotas.
* Tem permissão suficiente para definir delegação na subnet de integração? Durante a configuração regional de Integração VNet, a sua subnet de integração é delegada na Microsoft.Web. O VNet Integration UI delega a subnet para Microsoft.Web automaticamente. Se a sua conta não tiver permissões de networking suficientes para definir a delegação, precisará de alguém que possa definir atributos na sua subnet de integração para delegar a subnet. Para delegar manualmente a subnet de integração, vá à subnet UI da rede virtual Azure e desloque a delegação para microsoft.Web.

**Integração VNet exigida por gateway**
* O intervalo de endereços ponto-a-local nas gamas RFC 1918 (10.0.0.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0-192.168.255.255)?
* O portal mostra como estando no portal? Se o seu portal está para baixo, então traga-o de volta para cima.
* Os certificados mostram estar sincronizado, ou suspeita que a configuração da rede foi alterada?  Se os seus certificados estiverem dessincronizados ou suspeitar que foi feita uma alteração na configuração da sua rede virtual que não foi sincronizada com os seus ASPs, selecione **Sync Network**.
* Se estiver a atravessar uma VPN, a porta de entrada no local está configurada para encaminhar o tráfego de volta para Azure? Se conseguir chegar a pontos finais na sua rede virtual, mas não no local, verifique as suas rotas.
* Está a tentar usar um portal de coexistência que suporta tanto o ponto de ponta como o ExpressRoute? Os gateways de coexistência não são suportados com a Integração VNet.

Depurar problemas de networking é um desafio porque você não pode ver o que está bloqueando o acesso a uma combinação específica de hospedeiro:porta. Algumas causas incluem:

* Tem uma firewall no seu anfitrião que impede o acesso à porta de aplicação a partir da sua gama IP ponto-a-local. A travessia das subredes requer frequentemente acesso público.
* O seu anfitrião alvo está abatido.
* A sua candidatura está em baixo.
* Tinhao IP errado ou nome de anfitrião.
* A sua aplicação está a ouvir num porto diferente do que esperava. Pode comparar o seu ID de processo com a porta de audição utilizando "netstat -aon" no hospedeiro final.
* Os seus grupos de segurança de rede estão configurados de forma a impedir o acesso ao seu anfitrião de aplicação e porta a partir da sua gama IP ponto-a-local.

Não sabe qual o endereço que a sua aplicação realmente usa. Pode ser qualquer endereço na subnet de integração ou no intervalo de endereços ponto-a-local, por isso precisa de permitir o acesso a partir de toda a gama de endereços.

Os passos adicionais de depuração incluem:

* Ligue-se a um VM na sua rede virtual e tente chegar ao seu anfitrião de recursos:porta a partir daí. Para testar o acesso à TCP, utilize a **ligação**de teste de comando PowerShell . A sintaxe é:

      test-netconnection hostname [optional: -Port]

* Traga uma aplicação num VM e teste o acesso a esse anfitrião e à porta a partir da consola através da utilização de **tcpping**.

#### <a name="on-premises-resources"></a>Recursos no local ####

Se a sua aplicação não conseguir contactar um recurso no local, verifique se consegue contactar o recurso da sua rede virtual. Utilize o comando PowerShell de **ligação de rede de teste** para verificar se há acesso tCP. Se o seu VM não conseguir contactar o seu recurso no local, a sua ligação VPN ou ExpressRoute pode não estar configurada corretamente.

Se o seu VM virtual hospedado em rede pode chegar ao seu sistema no local, mas a sua aplicação não pode, a causa é provavelmente uma das seguintes razões:

* As suas rotas não estão configuradas com a sua subnet ou intervalos de endereços ponto-a-local no seu gateway no local.
* Os seus grupos de segurança de rede estão a bloquear o acesso à sua gama IP ponto-a-site.
* As suas firewalls no local estão a bloquear o tráfego da sua gama IP ponto-a-local.
* Está a tentar chegar a um endereço não RFC 1918 utilizando a funcionalidade regional de Integração VNet.