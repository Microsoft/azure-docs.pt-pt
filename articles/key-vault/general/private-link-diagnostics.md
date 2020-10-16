---
title: Diagnosticar problemas de configuração de links privados no Cofre da Chave Azure
description: Resolver problemas de ligações privadas comuns com Key Vault e mergulhar profundamente na configuração
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 156edbeda225b5457d6f5e7d29482e393b510736
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998394"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Diagnosticar problemas de configuração de links privados no Cofre da Chave Azure

## <a name="introduction"></a>Introdução

Este artigo ajuda os utilizadores a diagnosticar e corrigir problemas envolvendo o Cofre-Chave e a funcionalidade Links Privados. Este guia ajuda em aspetos de configuração, como obter links privados funcionando pela primeira vez, ou para corrigir uma situação em que as ligações privadas deixaram de funcionar por causa de alguma mudança.

Se é novo nesta funcionalidade, consulte [Integrate Key Vault com Azure Private Link](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>Problemas cobertos por este artigo

- As suas consultas de DNS ainda devolvem um endereço IP público para o cofre de chaves, em vez de um endereço IP privado que você esperaria de usar o recurso de links privados.
- Todos os pedidos feitos por um determinado cliente que está a usar link privado, estão a falhar com intervalos ou erros de rede, e o problema não é intermitente.
- O cofre tem um endereço IP privado, mas os pedidos ainda obtêm `403` resposta com o código de erro `ForbiddenByFirewall` interno.
- Está a usar links privados, mas o seu cofre ainda aceita pedidos da Internet pública.
- O seu cofre tem dois Pontos Finais Privados. Os pedidos de utilização de um estão a funcionar bem, mas os pedidos que usam o outro estão a falhar.
- Você tem outra subscrição, cofre chave ou rede virtual que está usando links privados. Queres fazer uma nova implantação semelhante, mas não consegues que as ligações privadas funcionem lá.

### <a name="problems-not-covered-by-this-article"></a>Problemas NÃO cobertos por este artigo

- Há um problema de conectividade intermitente. Num dado cliente, vemos alguns pedidos a funcionar e outros a não funcionar. *Os problemas intermitentes não são normalmente causados por um problema na configuração de links privados; são um sinal de rede ou sobrecarga de clientes.*
- Está a utilizar um produto Azure que suporta a BYOK (Bring Your Own Key), CMK (Customer Managed Keys) ou acesso a segredos armazenados no cofre de chaves. Quando ativa a firewall nas definições do cofre das chaves, este produto não pode aceder ao cofre da chave. *Veja a documentação específica do produto. Certifique-se de que indica explicitamente o suporte para cofres-chave com a firewall ativada. Suporte de contacto para esse produto específico, se necessário.*

### <a name="how-to-read-this-article"></a>Como ler este artigo

Se você é novo em links privados ou está a avaliar uma implementação complexa, é recomendável que você leia todo o artigo. Caso contrário, sinta-se à vontade para escolher a secção que faça mais sentido para o problema que está a enfrentar.

Vamos começar!

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. Confirme que é dono da ligação ao cliente

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Confirme que o seu cliente funciona na rede virtual

Este guia destina-se a ajudá-lo a fixar ligações ao cofre chave originário do código de aplicação. Exemplos são aplicações e scripts que executam em Azure Virtual Machines, clusters de tecido de serviço Azure, Azure App Service, Azure Kubernetes Service (AKS) e outros similares. Este guia também é aplicável aos acessos realizados na interface de utilizador do portal Azure, onde o navegador acede diretamente ao cofre da chave.

Por definição de links privados, a aplicação, o script ou o portal devem estar a funcionar em máquina, cluster ou ambiente ligado à Rede Virtual onde o [recurso Private Endpoint](../../private-link/private-endpoint-overview.md) foi implantado.

Se a aplicação, o script ou o portal estiverem a funcionar numa rede arbitrária ligada à Internet, este guia NÃO é aplicável e provavelmente não podem ser utilizados links privados. Esta limitação também é aplicável aos comandos executados no Azure Cloud Shell, uma vez que funcionam numa máquina Azure remota fornecida a pedido em vez do navegador do utilizador.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Se utilizar uma solução gerida, consulte documentação específica

Este guia NÃO é aplicável a soluções que são geridas pela Microsoft, onde o cofre-chave é acedido por um produto Azure que existe independentemente da Rede Virtual do cliente. Exemplos de tais cenários são Azure Storage ou Azure SQL configurados para encriptação em repouso, Azure Event Hub encriptando dados com chaves fornecidas pelo cliente, Azure Data Factory acedendo credenciais de serviço armazenadas no cofre chave, Azure Pipelines recuperando segredos do cofre chave, e outros cenários semelhantes. Nestes casos, *deve verificar se o produto suporta cofres-chave com a firewall ativada*. Este suporte é normalmente realizado com a funcionalidade [Serviços Fidedignos](overview-vnet-service-endpoints.md#trusted-services) da firewall Key Vault. No entanto, muitos produtos não estão incluídos na lista de serviços fidedignos, por uma variedade de razões. Nesse caso, alcance o suporte específico do produto.

Um pequeno número de produtos Azure apoia o conceito de injeção de *vnet.* Em termos simples, o produto adiciona um dispositivo de rede à Rede Virtual do cliente, permitindo-lhe enviar pedidos como se fosse implantado na Rede Virtual. Um exemplo notável é [Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Produtos como este podem fazer pedidos para o cofre de chaves usando os links privados, e este guia de resolução de problemas pode ajudar.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Confirmar que a ligação é aprovada e bem sucedida

Os seguintes passos validam que a ligação de ponto final privado é aprovada e conseguida:

1. Abra o portal Azure e abra o seu recurso de cofre chave.
2. No menu esquerdo, selecione **Networking**.
3. Clique no separador **ligações de ponto final privado.** Isto mostrará todas as ligações privadas de ponto final e respetivos estados. Se não houver ligações, ou se faltar a ligação para a sua Rede Virtual, terá de criar um novo Ponto Final Privado. Isto será coberto mais tarde.
4. Ainda em **ligações privadas**de ponto final , encontre o que está a diagnosticar e confirme que o "Estado de ligação" é **aprovado** e que "Estado de provisionamento" é **bem sucedido**.
    - Se a ligação estiver em estado "pendente", poderá simplesmente aprová-la.
    - Se a ligação "Rejeitada", "Falhada", "Erro", "Desligado" ou outro estado, então não é eficaz, tem de criar um novo recurso Private Endpoint.

É uma boa ideia apagar ligações ineficazes para manter as coisas limpas.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Confirme que a firewall do cofre da chave está devidamente configurada

>[!IMPORTANT]
> Alterar as definições de firewall pode remover o acesso de clientes legítimos que ainda não estão a usar links privados. Certifique-se de que está ciente das implicações de cada alteração na configuração da firewall.

Uma noção importante é que a funcionalidade de links privados só *dá* acesso ao cofre chave numa Rede Virtual que está fechada para evitar a exfiltração de dados. Não *remove* qualquer acesso existente. Para bloquear eficazmente os acessos a partir da Internet pública, deve ativar explicitamente a firewall do cofre da chave:

1. Abra o portal Azure e abra o seu recurso de cofre chave.
2. No menu esquerdo, selecione **Networking**.
3. Certifique-se de que o separador **Firewalls e redes virtuais** está selecionado no topo.
4. Certifique-se de que a opção **Ponto final privado e redes selecionadas** estão selecionadas. Se encontrar **todas as redes** selecionadas, isso explica porque é que os clientes externos ainda conseguem aceder ao cofre da chave.

As seguintes declarações aplicam-se também às definições de firewall:

- A funcionalidade de links privados não requer que qualquer "rede virtual" seja especificada nas definições de firewall do cofre de chaves. Todos os pedidos que utilizem o endereço IP privado do cofre de chaves (ver secção seguinte) devem funcionar, mesmo que nenhuma rede virtual seja especificada nas definições de firewall do cofre chave.
- A funcionalidade de links privados não requer especificar qualquer endereço IP nas definições de firewall do cofre chave. Mais uma vez, todos os pedidos que utilizem o endereço IP privado do cofre-chave devem funcionar, mesmo que nenhum endereço IP tenha sido especificado nas definições de firewall.

Se estiver a utilizar links privados, as únicas motivações para especificar a rede virtual ou o endereço IP na firewall do cofre chave são:

- Você tem um sistema híbrido onde alguns clientes usam links privados, alguns usam pontos finais de serviço, alguns usam endereço IP público.
- Estás a transitar para ligações privadas. Neste caso, uma vez confirmado que todos os clientes estão a utilizar links privados, deverá remover redes virtuais e endereços IP das definições de firewall do cofre chave.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Certifique-se de que o cofre tem um endereço IP privado

### <a name="difference-between-private-and-public-ip-addresses"></a>Diferença entre endereços IP privados e públicos

Um endereço IP privado tem sempre um dos seguintes formatos:

- 10.x.x.x: Exemplos: `10.1.2.3` `10.56.34.12` . . .
- 172.16.x.x a 172.32.x.x: Exemplos: `172.20.1.1` `172.31.67.89` . .
- 192.168.x.x: `192.168.0.1` Exemplos: `192.168.100.7`

Alguns endereços e intervalos IP são reservados:

- 224.x.x.x: Multicast
- 255.255.255.255: Transmissão
- 127.x.x.x: Loopback
- 169.254.x.x: Link-local
- 168.63.129.16: DNS internos

Todos os outros endereços IP são públicos.

Quando navegar no portal ou executar um comando que mostre o endereço IP, certifique-se de que pode identificar se esse endereço IP é privado, público ou reservado. Para que as ligações privadas funcionem, o nome de anfitrião do cofre-chave deve ser resolvido para um endereço IP privado pertencente ao espaço de endereço de Rede Virtual.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Encontre o endereço IP privado do cofre chave na rede virtual

Você precisará diagnosticar a resolução do nome de anfitrião, e para isso você deve saber o endereço IP privado exato do seu cofre chave com links privados ativados. Para encontrar esse endereço, siga este procedimento:

1. Abra o portal Azure e abra o seu recurso de cofre chave.
2. No menu esquerdo, selecione **Networking**.
3. Clique no separador **ligações de ponto final privado.** Isto mostrará todas as ligações privadas de ponto final e respetivos estados.
4. Encontre o que está a diagnosticar e confirme que o "Estado de ligação" é **aprovado** e que o estado de provisionamento é **bem sucedido.** Se não estiver a ver isto, volte às secções anteriores deste documento.
5. Quando encontrar o item certo, clique no link na coluna **ponto final Privado.** Isto abrirá o recurso Private Endpoint.
6. A página 'Vista Geral' pode mostrar uma secção chamada **definições de DNS personalizados**. Confirme que há apenas uma entrada que corresponde ao nome de anfitrião do cofre da chave. A entrada mostra o endereço IP privado do cofre.
7. Pode também clicar no link na **interface Da Rede** e confirmar que o endereço IP privado é o mesmo exibido no passo anterior. A interface de rede é um dispositivo virtual que representa o cofre de chaves.

O endereço IP é o que os VMs e outros dispositivos *que executam na mesma Rede Virtual* utilizarão para ligar ao cofre de chaves. Tome nota do endereço IP, ou mantenha o separador do navegador aberto e não toque nele enquanto faz mais investigações.

>[!NOTE]
> Se o seu cofre-chave tiver vários pontos finais privados, então tem vários endereços IP privados. Isto só é útil se tiver várias Redes Virtuais a aceder ao mesmo cofre de chaves, cada uma através do seu próprio Ponto Final Privado (o Ponto Final Privado pertence a uma única Rede Virtual). Certifique-se de diagnosticar o problema para a Rede Virtual correta e selecione a ligação de ponto final privado correta no procedimento acima. Além disso, **não** crie vários Pontos Finais Privados para o mesmo Cofre de Chaves na mesma Rede Virtual. Isto não é necessário e constitui uma fonte de confusão.

## <a name="5-validate-the-dns-resolution"></a>5. Validar a resolução do DNS

A resolução do DNS é o processo de tradução do nome de anfitrião do cofre chave (exemplo: `fabrikam.vault.azure.net` ) para um endereço IP (exemplo: `10.1.2.3` ). As seguintes subsecções mostram os resultados esperados da resolução do DNS em cada cenário.

### <a name="key-vaults-without-private-link"></a>Cofres chave sem ligação privada

Esta secção destina-se a fins de aprendizagem. Quando o cofre-chave não tem nenhuma ligação privada de ponto final em estado aprovado, a resolução do nome hospedeiro dá um resultado semelhante a este:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

Pode ver que o nome se resolve para um endereço IP público, e não há `privatelink` pseudónimo. O pseudónimo é explicado mais tarde, não se preocupe agora.

Espera-se o resultado acima, independentemente da máquina ser ligada à Rede Virtual ou ser uma máquina arbitrária com ligação à Internet. Isto acontece porque o cofre-chave não tem nenhuma ligação privada de ponto final em estado aprovado, e, portanto, não há necessidade do cofre chave para suportar ligações privadas.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Cofre chave com ligação privada resolvendo a partir de máquina de Internet arbitrária

Quando o cofre-chave tiver uma ou mais ligações privadas de ponto final em estado aprovado e resolver o nome de anfitrião a partir de uma máquina arbitrária ligada à Internet (uma máquina que *não está* ligada à Rede Virtual onde reside o Ponto Final Privado), encontrará isto:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

A notável diferença do cenário anterior é que há um novo pseudónimo com o valor `{vaultname}.privatelink.vaultcore.azure.net` . Isto significa que o cofre-chave Data Plane está pronto para aceitar pedidos de links privados.

Não significa que os pedidos realizados a partir de máquinas *fora* da Rede Virtual (como a que acabou de usar) utilizem links privados - não irão. Pode ver isso pelo facto de o nome de anfitrião ainda resolver um endereço IP público. Apenas as máquinas *ligadas à Rede Virtual* podem utilizar links privados. Mais sobre isto seguir-se-á.

Se não vir o `privatelink` pseudónimo, significa que o cofre tem zero ligações privadas no `Approved` estado. Volte para [esta secção](#2-confirm-that-the-connection-is-approved-and-succeeded) antes de voltar a tentar.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Cofre chave com ligação privada resolvendo a partir de Rede Virtual

Quando o cofre-chave tem uma ou mais ligações privadas de ponto final em estado aprovado e resolve o nome de anfitrião de uma máquina ligada à Rede Virtual onde o Ponto Final Privado foi criado, esta é a resposta esperada:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  10.1.2.3
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3

Há duas diferenças notáveis. Primeiro, o nome resolve-se para um endereço IP privado. Este deve ser o endereço IP que encontramos na [secção correspondente](#find-the-key-vault-private-ip-address-in-the-virtual-network) deste artigo. Segundo, não há outros pseudónimos depois `privatelink` do um. Isto acontece porque os servidores DNS da Rede Virtual *intercetam* a cadeia de pseudónimos e devolvem o endereço IP privado diretamente do nome `fabrikam.privatelink.vaultcore.azure.net` . Essa entrada é na verdade um `A` recorde numa Zona Privada de DNS. Mais sobre isto seguir-se-á.

>[!NOTE]
> O resultado acima só acontece numa Máquina Virtual ligada à Rede Virtual onde foi criado o Ponto Final Privado. Se não tiver um VM implantado na Rede Virtual que contenha o Ponto Final Privado, desloque um e conecte-o remotamente, então execute o `nslookup` comando (Windows) ou o `host` comando (Linux) acima.

Se executar estes comandos numa Máquina Virtual ligada à Rede Virtual onde foi criado o Ponto Final Privado e **não** mostrar o endereço IP privado do cofre chave, a próxima secção poderá ajudar a corrigir o problema.

## <a name="6-validate-the-private-dns-zone"></a>6. Validar a Zona Privada de DNS

Se a resolução do DNS não estiver a funcionar como descrito na secção anterior, pode haver um problema com a sua Zona Privada de DNS e esta secção pode ajudar. Se a resolução DNS mostrar o endereço IP privado do cofre correto, a sua Zona Privada de DNS provavelmente está correta. Podes saltar toda esta secção.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Confirme que existe o recurso privado de DNS Zone

A sua subscrição Azure deve ter um recurso [privado da Zona DNS](../../dns/private-dns-privatednszone.md) com este nome exato:

    privatelink.vaultcore.azure.net

Pode verificar a presença deste recurso indo para a página de subscrição no Portal e selecionando "Recursos" no menu esquerdo. O nome do recurso deve ser `privatelink.vaultcore.azure.net` , e o tipo de recurso deve ser zona privada de **DNS**.

Normalmente, este recurso é criado automaticamente quando cria um Ponto Final Privado utilizando um procedimento comum. Mas há casos em que este recurso não é criado automaticamente e tem de o fazer manualmente. Este recurso pode ter sido acidentalmente apagado também.

Se não tiver este recurso, crie um novo recurso Private DNS Zone na sua subscrição. Lembre-se que o nome deve ser exatamente `privatelink.vaultcore.azure.net` , sem espaços ou pontos adicionais. Se especificar o nome errado, a resolução de nomes explicada neste artigo não funcionará. Para obter mais informações sobre como criar este recurso, consulte [Criar uma zona de DNS privada Azure utilizando o portal Azure](../../dns/private-dns-getstarted-portal.md). Se seguir essa página, pode ignorar a criação da Rede Virtual porque neste momento já deve ter uma. Também pode saltar procedimentos de validação com Máquinas Virtuais.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Confirme que a Zona Privada de DNS está ligada à Rede Virtual

Não basta ter uma Zona Privada de DNS. Também deve ser ligado à Rede Virtual que contém o Ponto Final Privado. Se a Zona Privada de DNS não estiver ligada à Rede Virtual correta, qualquer resolução DNS dessa Rede Virtual ignorará a Zona Privada de DNS.

Abra o recurso Zona DNS Privada e clique na opção **links de rede Virtual** no menu esquerdo. Isto mostrará uma lista de links, cada um com o nome de uma Rede Virtual na sua subscrição. A Rede Virtual que contém o recurso Private Endpoint deve ser listada aqui. Se não estiver lá, adicione. Para obter etapas detalhadas, consulte [Link the virtual network](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). Pode deixar "Ativar o registo automático" sem ser verificado - essa funcionalidade não está relacionada com links privados.

Uma vez que a Zona Privada de DNS esteja ligada à Rede Virtual, os pedidos dns originários da Rede Virtual procurarão nomes na Zona Privada do DNS. Isto é necessário para uma resolução de endereços correta realizada em Máquinas Virtuais ligadas à Rede Virtual onde o Ponto Final Privado foi criado.

>[!NOTE]
> Se acabou de guardar o link, pode demorar algum tempo para que isto entre em vigor, mesmo depois de o Portal dizer que a operação está concluída. Também poderá ter de reiniciar o VM que está a usar para testar a resolução de DNS.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Confirme que a Zona Privada de DNS contém o registo certo A

Utilizando o Portal, abra a Zona Privada de DNS com o nome `privatelink.vaultcore.azure.net` . A página "Vista Geral" mostra todos os registos. Por predefinição, haverá um registo com nome `@` e `SOA` tipo, que significa Início da Autoridade. Não toques nisso.

Para que a resolução do nome do cofre funcione, deve haver um `A` registo com o nome do cofre simples sem sufixos ou pontos. Por exemplo, se o nome de anfitrião for `fabrikam.vault.azure.net` , deve haver um registo com o nome , sem `A` `fabrikam` sufixos ou pontos.

Além disso, o valor do `A` registo (o endereço IP) deve ser [o endereço IP privado do cofre chave](#find-the-key-vault-private-ip-address-in-the-virtual-network). Se encontrar o `A` registo mas contiver o endereço IP errado, deve remover o endereço IP errado e adicionar um novo. Recomenda-se que retire todo o `A` registo e adicione um novo.

>[!NOTE]
> Sempre que remover ou modificar um `A` registo, a máquina pode ainda resolver o antigo endereço IP porque o valor TTL (Tempo para Viver) pode ainda não estar expirado. Recomenda-se que especifique sempre um valor TTL não inferior a 60 segundos (um minuto) e não superior a 600 segundos (10 minutos). Se especificar um valor demasiado grande, os seus clientes podem demorar muito tempo a recuperar de interrupções.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>Resolução DNS para mais de uma Rede Virtual

Se existem várias Redes Virtuais e cada um tem o seu próprio recurso Private Endpoint, referindo-se ao mesmo cofre-chave, então o nome de anfitrião do cofre-chave precisa de ser resolvido para um endereço IP privado diferente dependendo da rede. Isto significa que são também necessárias várias Zonas Privadas de DNS, cada uma ligada a uma rede virtual diferente e usando um endereço IP diferente no `A` registo.

Em cenários mais avançados, as Redes Virtuais podem ter o seu olhar ativado. Neste caso, apenas uma Rede Virtual necessita do recurso Private Endpoint, embora ambos possam ter de estar ligados ao recurso Private DNS Zone. Este cenário não é diretamente abrangido por este documento.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>Entenda que tem controlo sobre a resolução do DNS

Como explicado na [secção anterior,](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)um cofre-chave com ligações privadas tem o pseudónimo `{vaultname}.privatelink.vaultcore.azure.net` no seu registo *público.* O servidor DNS utilizado pela Rede Virtual utiliza o registo público, mas verifica todos os pseudónimos para um registo *privado,* e se for encontrado, deixará de seguir pseudónimos definidos no registo público.

Esta lógica significa que se a Rede Virtual estiver ligada a uma Zona Privada de DNS com `privatelink.vaultcore.azure.net` nome, e o registo público de DNS para o cofre-chave tem o pseudónimo `fabrikam.privatelink.vaultcore.azure.net` (note que o sufixo do cofre de porta-chaves corresponde exatamente ao nome da Zona De DNS Privada), então a consulta dns procurará um `A` registo com nome na Zona Privada do `fabrikam` *DNS*. Se o `A` registo for encontrado, o seu endereço IP é devolvido na consulta DNS, e nenhum outro exame é realizado no registo público de DNS.

Como pode ver, a resolução do nome está sob o seu controlo. Os raciocínios para este desenho são:

- Pode ter um cenário complexo que envolve servidores DNS personalizados e integração com redes no local. Nesse caso, é necessário controlar a tradução dos nomes para endereços IP.
- Pode ter de aceder a um cofre sem ligações privadas. Nesse caso, a resolução do nome de anfitrião da Rede Virtual deve devolver o endereço IP público, o que acontece porque os cofres-chave sem links privados não têm o `privatelink` pseudónimo no registo de nome.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. Validar que os pedidos para o cofre chave usar link privado

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Consulta o `/healthstatus` ponto final do cofre chave

O cofre da chave fornece o `/healthstatus` ponto final, que pode ser usado para diagnósticos. Os cabeçalhos de resposta incluem o endereço IP de origem, como visto pelo serviço de cofre chave. Pode chamar esse ponto final com o seguinte comando **(lembre-se de usar o nome de anfitrião do cofre**da chave):

Janelas (PowerShell):

    PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers

    Key                           Value
    ---                           -----
    Pragma                        no-cache
    x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
    x-ms-keyvault-service-version 1.2.27.0
    x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security     max-age=31536000;includeSubDomains
    Content-Length                4
    Cache-Control                 no-cache
    Content-Type                  application/json; charset=utf-8

Linux, ou uma versão recente do Windows 10 que `curl` inclui:

    joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
    x-ms-keyvault-service-version: 1.2.27.0
    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security: max-age=31536000;includeSubDomains
    Content-Length: 4

Se não estiver a obter uma saída semelhante a essa, ou se tiver um erro de rede, significa que o cofre da chave não está acessível através do nome de anfitrião especificado `fabrikam.vault.azure.net` (no exemplo). Ou o nome de anfitrião não está a resolver o endereço IP correto, ou tem um problema de conectividade na camada de transporte. Pode ser causado por problemas de encaminhamento, quedas de pacotes e outras razões. Tem que investigar mais.

A resposta deve incluir o `x-ms-keyvault-network-info` cabeçalho:

    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;

O `addr` campo no `x-ms-keyvault-network-info` cabeçalho mostra o endereço IP da origem do pedido. Este endereço IP pode ser um dos seguintes:

- O endereço IP privado da máquina que faz o pedido. Isto indica que o pedido está a utilizar links privados ou pontos finais de serviço. Este é o resultado esperado para ligações privadas.
- Algum outro endereço IP privado, *não* da máquina que faz o pedido. Isto indica que algum encaminhamento personalizado é eficaz. Indica ainda que o pedido está a utilizar links privados ou pontos finais de serviço.
- Algum endereço IP público. Isto indica que o pedido está a ser encaminhado para a Internet através de um dispositivo de gateway (NAT). Isto indica que o pedido NÃO está a utilizar links privados, e que alguma questão tem de ser corrigida. As razões comuns para tal são 1) o ponto final privado não está em estado aprovado e bem sucedido; e 2) o nome de anfitrião não está a resolver o endereço IP privado do cofre. Este artigo inclui ações de resolução de problemas para ambos os casos.

>[!NOTE]
> Se o pedido `/healthstatus` de funcionamento, mas o `x-ms-keyvault-network-info` cabeçalho está desaparecido, então o ponto final provavelmente não será servido pelo cofre chave. Uma vez que os comandos acima também validam o certificado HTTPS, o cabeçalho em falta pode ser um sinal de adulteração.

### <a name="query-the-key-vault-ip-address-directly"></a>Consultar o endereço IP do cofre chave diretamente

>[!IMPORTANT]
> Aceder ao cofre sem validação do certificado HTTPS é perigoso e só pode ser utilizado para fins de aprendizagem. O código de produção nunca deve aceder ao cofre sem esta validação do lado do cliente. Mesmo que esteja apenas a diagnosticar problemas, poderá estar sujeito a tentativas de adulteração que não serão reveladas se desativar frequentemente a validação do certificado HTTPS nos seus pedidos para o cofre chave.

Se instalou uma versão recente do PowerShell, pode usar para ignorar as verificações de `-SkipCertificateCheck` certificados HTTPS, então pode direcionar o [endereço IP](#find-the-key-vault-private-ip-address-in-the-virtual-network) do cofre chave diretamente:

    PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers

Se estiver a `curl` usar, pode fazer o mesmo com o `-k` argumento:

    joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus

As respostas devem ser as mesmas da secção anterior, o que significa que deve incluir o `x-ms-keyvault-network-info` cabeçalho com o mesmo valor. O `/healthstatus` ponto final não se importa se está a usar o nome de anfitrião do cofre ou o endereço IP do cofre.

Se vir `x-ms-keyvault-network-info` a devolução de um valor para o pedido utilizando o nome de anfitrião do cofre de chaves e outro valor para o pedido utilizando o endereço IP, cada pedido está direcionado para um ponto final diferente. Consulte a explicação do `addr` campo `x-ms-keyvault-network-info` da secção anterior, para decidir qual o caso que está errado e precisa de ser corrigido.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. Outras alterações e personalizações que causam impacto

Os seguintes itens são ações de investigação não exaustivas. Eles dir-lhe-ão onde procurar questões adicionais, mas você deve ter conhecimento avançado de rede para corrigir problemas nestes cenários.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Diagnosticar servidores DNS personalizados na Rede Virtual

No Portal, abra o recurso Rede Virtual. No menu esquerdo, abra **os servidores DNS**. Se estiver a utilizar "Personalizado", então a resolução dns pode não ser como descrito neste documento. Tem de diagnosticar como os seus servidores DNS estão a resolver o nome de anfitrião do cofre.

Se estiver a utilizar os servidores DNS fornecidos pelo Azure, todo este documento é aplicável.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Diagnosticar anfitriões sobressaídos ou servidores DNS personalizados na Máquina Virtual

Muitos sistemas operativos permitem a definição de um endereço IP fixo explícito por nome de anfitrião, normalmente alterando o `hosts` ficheiro. Também podem permitir a sobredição dos servidores DNS. Se utilizar um destes cenários, proceda com opções de diagnóstico específicas do sistema.

### <a name="promiscuous-proxies-fiddler-etc"></a>Proxies promíscuos (Violinista, etc.)

Exceto quando explicitamente notado, as opções de diagnóstico neste artigo só funcionam se não houver procuração promíscua presente no ambiente. Embora estes proxies sejam muitas vezes instalados exclusivamente na máquina que está a ser diagnosticada (o Violinista é o exemplo mais comum), os administradores avançados podem substituir as autoridades de certificados de raiz (AC) e instalar um proxy promíscuo em dispositivos de gateway que servem várias máquinas na rede. Estes proxies podem afetar substancialmente a segurança e a fiabilidade. A Microsoft não suporta configurações que utilizem estes produtos.

### <a name="other-things-that-may-affect-connectivity"></a>Outras coisas que podem afetar a conectividade

Esta é uma lista não exaustiva de itens que podem ser encontrados em cenários avançados ou complexos:

- Definições de firewall, quer a Firewall Azure ligada à Rede Virtual, quer uma solução de firewall personalizada implantada na Rede Virtual ou na máquina.
- O espreitamento da rede, que pode ter impacto nos servidores DNS utilizados e na forma como o tráfego é encaminhado.
- Soluções personalizadas de gateway (NAT), que podem afetar a forma como o tráfego é encaminhado, incluindo o tráfego de consultas de DNS.
