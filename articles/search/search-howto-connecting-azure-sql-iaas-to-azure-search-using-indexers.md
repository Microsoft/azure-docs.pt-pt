---
title: Conexão Azure SQL VM para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Ative as ligações encriptadas e configuure a firewall para permitir ligações ao SQL Server numa máquina virtual Azure (VM) a partir de um indexante na Azure Cognitive Search.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 1f9169d4f3f6361e557c41a4d612cf6c439257fb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722519"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configure uma ligação de um indexante de pesquisa cognitiva Azure para SQL Server em um VM Azure

Ao configurar um [indexador Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) para extrair conteúdo de uma base de dados numa máquina virtual Azure, são necessários passos adicionais para ligações seguras. 

Uma ligação da Azure Cognitive Search ao SQL Server numa máquina virtual é uma ligação pública à Internet. Para que as ligações seguras tenham êxito, complete os seguintes passos:

+ Obtenha um certificado de um fornecedor da [Autoridade de Certificados](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para o nome de domínio totalmente qualificado da instância sql Server na máquina virtual

+ Instale o certificado na máquina virtual e, em seguida, ative e configuure as ligações encriptadas no VM utilizando as instruções deste artigo.

## <a name="enable-encrypted-connections"></a>Ativar ligações encriptadas

A Azure Cognitive Search requer um canal encriptado para todos os pedidos de indexante através de uma ligação pública à Internet. Esta secção lista os passos para que isto funcione.

1. Verifique as propriedades do certificado para verificar se o nome do sujeito é o nome de domínio totalmente qualificado (FQDN) do Azure VM. Pode utilizar uma ferramenta como a CertUtils ou os Certificados para visualizar as propriedades. Pode obter o FQDN da secção Essencial da lâmina de serviço VM, no campo **de etiquetas de identificação IP/DNS público,** no [portal Azure](https://portal.azure.com/).
  
   + Para VMs criados usando o modelo mais recente **de Gestor de Recursos,** o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`

   + Para os VMs mais antigos criados como um VM **clássico,** o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>` .

1. Configure o SqL Server para utilizar o certificado utilizando o Editor de Registo (regedit). 

   Embora o Gestor de Configuração do Servidor SQL seja frequentemente utilizado para esta tarefa, não é possível usá-lo para este cenário. Não encontrará o certificado importado porque o FQDN do VM em Azure não corresponde ao FQDN determinado pelo VM (identifica o domínio como o computador local ou o domínio da rede ao qual se junta). Quando os nomes não corresponderem, use o regedit para especificar o certificado.

   + Em regedit, navegue por esta chave de registo: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate` .

     A `[MSSQL13.MSSQLSERVER]` peça varia com base na versão e no nome da instância. 

   + Desajuste o valor da chave **certificado** à **impressão digital** do certificado TLS/SSL que importou para o VM.

     Há várias maneiras de obter a impressão digital, algumas melhores que outras. Se o copiar a partir do snap-in **certificados** no MMC, provavelmente irá captar um personagem principal [invisível, conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), o que resulta num erro quando se tenta uma ligação. Existem várias soluções alternativas para corrigir este problema. O mais fácil é retro-espaço e, em seguida, reescrever o primeiro personagem da impressão digital para remover o personagem principal no campo de valor chave no regedit. Em alternativa, pode utilizar uma ferramenta diferente para copiar a impressão digital.

1. Conceder permissões à conta de serviço. 

    Certifique-se de que a conta de serviço SQL Server recebe a permissão adequada na chave privada do certificado TLS/SSL. Se ignorar este passo, o SQL Server não arrancará. Pode utilizar os **Certificados** snap-in ou **CertUtils** para esta tarefa.

1. Reinicie o serviço do SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conectividade do Servidor SQL no VM

Depois de configurar a ligação encriptada exigida pela Azure Cognitive Search, existem passos de configuração adicionais intrínsecos ao SQL Server em VMs Azure. Se ainda não o fez, o próximo passo é terminar a configuração usando qualquer um destes artigos:

+ Para um **Gestor de Recursos** VM, consulte Connect to a [SQL Server Virtual Machine on Azure utilizando o Resource Manager](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 

+ Para um VM **clássico,** consulte [Connect to a SQL Server Virtual Machine on Azure Classic](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

Em particular, reveja a secção de cada artigo para "ligar através da internet".

## <a name="configure-the-network-security-group-nsg"></a>Configure o Grupo de Segurança da Rede (NSG)

Não é incomum configurar o NSG e a correspondente Lista de Controlo de Acesso ou Acesso (ACL) para tornar o seu VM Azure acessível a outras partes. É provável que já tenha feito isto antes para permitir que a sua própria lógica de aplicação se conecte ao seu SQL Azure VM. Não é diferente para uma ligação Azure Cognitive Search ao seu SQL Azure VM. 

Os links abaixo fornecem instruções sobre a configuração NSG para implementações VM. Utilize estas instruções para a ACL um ponto final de pesquisa cognitiva Azure com base no seu endereço IP.

> [!NOTE]
> Para obter antecedentes, veja [o que é um Grupo de Segurança de Rede?](../virtual-network/network-security-groups-overview.md)

+ Para um **VM gestor de recursos,** consulte [como criar NSGs para implementações ARM](../virtual-network/tutorial-filter-network-traffic.md).

+ Para um VM **clássico,** consulte [como criar NSGs para implementações clássicas.](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps)

A abordagem IP pode colocar alguns desafios que são facilmente ultrapassados se você estiver ciente do problema e potenciais soluções alternativas. As restantes secções fornecem recomendações para lidar com questões relacionadas com endereços IP na ACL.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Restringir o acesso à Pesquisa Cognitiva Azure

Recomendamos vivamente que restringa o acesso ao endereço IP do seu serviço de pesquisa e à gama de endereços IP da etiqueta de `AzureCognitiveSearch` [serviço](../virtual-network/service-tags-overview.md#available-service-tags) na ACL em vez de tornar os seus VMs SQL Azure abertos a todos os pedidos de ligação.

Pode descobrir o endereço IP, pingando o FQDN (por `<your-search-service-name>.search.windows.net` exemplo,) do seu serviço de pesquisa. Embora seja possível que o endereço IP do serviço de pesquisa seja alterado, é improvável que este se altere. O endereço IP tende a ser estático durante toda a vida do serviço.

Pode descobrir a gama de endereços IP da tag de `AzureCognitiveSearch` [serviço](../virtual-network/service-tags-overview.md#available-service-tags) utilizando [ficheiros JSON transferíveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou através da [API de Descoberta de Marca de Serviço.](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) O intervalo de endereços IP é atualizado semanalmente.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Incluir os endereços IP do portal de pesquisa cognitiva Azure

Se estiver a utilizar o portal Azure para criar um indexante, a lógica do portal Azure Cognitive Search também precisa de acesso ao seu SQL Azure VM durante o tempo de criação. Os endereços IP do portal de pesquisa cognitiva Azure podem ser encontrados através de `stamp2.search.ext.azure.com` pinging , que é o domínio do gestor de tráfego.

Os aglomerados em diferentes regiões ligam-se a este gestor de tráfego. O ping pode devolver o endereço IP e o domínio de `stamp2.search.ext.azure.com` , mas se o seu serviço estiver numa região diferente, o nome IP e de domínio será diferente. O endereço IP devolvido do ping é o correto para o portal Azure na sua região.

## <a name="next-steps"></a>Passos seguintes

Com a configuração fora do caminho, pode agora especificar um SQL Server no Azure VM como a fonte de dados para um indexante de Pesquisa Cognitiva Azure. Para obter mais informações, consulte [a Connecting Azure SQL Database à Azure Cognitive Search utilizando indexantes](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).