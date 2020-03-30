---
title: Conexão VM Azure SQL para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Ative ligações encriptadas e configure a firewall para permitir ligações ao SQL Server numa máquina virtual Azure (VM) a partir de um indexante em Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1ab2b7860e8a75da5f8acef2fc4fa54d4b73a30d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256968"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configure uma ligação de um indexador de pesquisa cognitiva Azure ao Servidor SQL num VM Azure

Como notado na [Connecting Azure SQL Database to Azure Cognitive Search utilizando indexers,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)a criação de indexantes contra o **SQL Server em VMs Azure** (ou **SQL Azure VMs** para abreviar) é suportado pela Azure Cognitive Search, mas existem alguns pré-requisitos relacionados com a segurança para cuidar primeiro. 

As ligações da Pesquisa Cognitiva Azure ao Servidor SQL num VM são uma ligação à internet pública. Todas as medidas de segurança que normalmente seguiria para estas ligações também se aplicam aqui:

+ Obtenha um certificado de um fornecedor de Autoridade de [Certificados](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para o nome de domínio totalmente qualificado da instância SQL Server no VM Azure.
+ Instale o certificado no VM e, em seguida, ative e configure ligações encriptadas no VM utilizando as instruções deste artigo.

## <a name="enable-encrypted-connections"></a>Ativar ligações encriptadas
A Pesquisa Cognitiva Azure requer um canal encriptado para todos os pedidos indexantes através de uma ligação à internet pública. Esta secção enumera os passos para que isto funcione.

1. Verifique se as propriedades do certificado para verificar se o nome do assunto é o nome de domínio totalmente qualificado (FQDN) do VM Azure. Pode utilizar uma ferramenta como a CertUtils ou os Certificados para visualizar as propriedades. Pode obter o FQDN na secção Essencial da lâmina de serviço VM, no campo de etiquetas de **endereço IP/DNS público,** no [portal Azure](https://portal.azure.com/).
   
   * Para VMs criados usando o modelo mais recente de Gestor de **Recursos,** o FQDN é formatado como`<your-VM-name>.<region>.cloudapp.azure.com`
   * Para VMs mais antigos criados como um VM `<your-cloud-service-name.cloudapp.net>` **clássico,** o FQDN é formatado como .

2. Configure o Servidor SQL para utilizar o certificado utilizando o Editor de Registo (regedit). 
   
    Embora o SQL Server Configuration Manager seja frequentemente utilizado para esta tarefa, não pode usá-lo para este cenário. Não encontrará o certificado importado porque o FQDN do VM em Azure não corresponde ao FQDN determinado pelo VM (identifica o domínio como computador local ou o domínio de rede ao qual está ligado). Quando os nomes não coincidirem, utilize o regedita para especificar o certificado.
   
   * No regedite, navegue para `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`esta chave de registo: .
     
     A `[MSSQL13.MSSQLSERVER]` peça varia em função da versão e do nome da instância. 
   * Detete o valor da chave **certificado** para a **impressão digital** do certificado TLS/SSL que importou para o VM.
     
     Há várias maneiras de obter a impressão digital, algumas melhores que outras. Se o copiar **espetado** nos Certificados no MMC, provavelmente irá captar um personagem principal [invisível, tal como descrito neste artigo](https://support.microsoft.com/kb/2023869/)de suporte, o que resulta num erro quando se tenta uma ligação. Existem várias sobras para corrigir este problema. O mais fácil é recolocar o backspace e, em seguida, reescrever o primeiro personagem da impressão digital para remover o personagem principal no campo de valor chave em regedit. Em alternativa, pode utilizar uma ferramenta diferente para copiar a impressão digital.

3. Conceda permissões à conta de serviço. 
   
    Certifique-se de que a conta de serviço SQL Server recebe permissão adequada na chave privada do certificado TLS/SSL. Se ignorar este passo, o Servidor SQL não arranca. Pode utilizar os **Certificados** snap-in ou **CertUtils** para esta tarefa.
    
4. Reinicie o serviço do SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configure a conectividade do Servidor SQL no VM
Depois de configurar a ligação encriptada exigida pela Pesquisa Cognitiva Azure, existem passos de configuração adicionais intrínsecos ao Servidor SQL em VMs Azure. Se ainda não o fez, o próximo passo é terminar a configuração usando qualquer um destes artigos:

* Para um VM gestor de **recursos,** consulte [Connect to a SQL Server Virtual Machine on Azure utilizando o Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Para um VM **clássico,** consulte [Connect to a SQL Server Virtual Machine on Azure Classic](../virtual-machines/windows/classic/sql-connect.md).

Em particular, reveja a secção em cada artigo para "ligar pela internet".

## <a name="configure-the-network-security-group-nsg"></a>Configure o Grupo de Segurança da Rede (NSG)
Não é incomum configurar o NSG e a correspondente Lista de Controlo de Acesso (ACL) para tornar o seu Azure VM acessível a outras partes. É provável que tenha feito isto antes para permitir que a sua própria lógica de aplicação se conectem ao seu SQL Azure VM. Não é diferente para uma ligação de pesquisa cognitiva Azure ao seu SQL Azure VM. 

Os links abaixo fornecem instruções sobre a configuração NSG para implementações VM. Utilize estas instruções para ACL um ponto final de pesquisa cognitiva Azure com base no seu endereço IP.

> [!NOTE]
> Para segundo plano, veja [o que é um Grupo de Segurança da Rede?](../virtual-network/security-overview.md)
> 
> 

* Para um VM gestor de **recursos,** consulte [como criar NSGs para implementações ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Para um VM **clássico,** veja [como criar NSGs para implementações clássicas.](../virtual-network/virtual-networks-create-nsg-classic-ps.md)

A abordagem ip pode colocar alguns desafios que são facilmente ultrapassados se você estiver ciente do problema e potenciais soluções. As restantes secções fornecem recomendações para tratar questões relacionadas com endereços IP na ACL.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Restringir o acesso à Pesquisa Cognitiva Azure
Recomendamos vivamente que restrinja o acesso ao endereço IP do `AzureCognitiveSearch` seu serviço de pesquisa e à gama de endereços IP da etiqueta de [serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) no ACL em vez de tornar os seus VMs SQL Azure abertos a todos os pedidos de ligação.

Pode descobrir o endereço IP através do sinal do FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) do seu serviço de pesquisa.

Pode descobrir a gama de `AzureCognitiveSearch` endereços IP da etiqueta de [serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) utilizando [ficheiros JSON descarregados](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) ou através da [API De identificação](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)de etiquetas de serviço . A gama de endereços IP é atualizada semanalmente.

#### <a name="managing-ip-address-fluctuations"></a>Gestão das flutuações do endereço IP
Se o seu serviço de pesquisa tiver apenas uma unidade de pesquisa (isto é, uma réplica e uma divisória), o endereço IP mudará durante o reinício do serviço de rotina, invalidando um ACL existente com o endereço IP do seu serviço de pesquisa.

Uma maneira de evitar o erro de conectividade subsequente é usar mais do que uma réplica e uma partição na Pesquisa Cognitiva Azure. Ao fazê-lo aumenta o custo, mas também resolve o problema do endereço IP. Na Pesquisa Cognitiva Azure, os endereços IP não mudam quando se tem mais de uma unidade de pesquisa.

Uma segunda abordagem é permitir que a ligação falhe e, em seguida, reconfigurar os ACLs no NSG. Em média, pode esperar que os endereços IP mudem a cada poucas semanas. Para os clientes que controlam a indexação numa base pouco frequente, esta abordagem pode ser viável.

Uma terceira abordagem viável (mas não particularmente segura) é especificar a gama de endereços IP da região de Azure onde o seu serviço de pesquisa é aprovisionado. A lista de intervalos ip a partir dos quais os endereços IP públicos são atribuídos aos recursos do Azure é publicada nas [gamas IP do Azure Datacenter.](https://www.microsoft.com/download/details.aspx?id=41653) 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Inclua os endereços IP do portal de pesquisa cognitiva Azure
Se estiver a usar o portal Azure para criar um indexante, a lógica do portal de pesquisa cognitiva Azure também precisa de acesso ao seu SQL Azure VM durante o tempo de criação. Os endereços IP do portal de pesquisa `stamp2.search.ext.azure.com`cognitiva Azure podem ser encontrados através de pinging .

## <a name="next-steps"></a>Passos seguintes
Com a configuração fora do caminho, pode agora especificar um Servidor SQL no Azure VM como a fonte de dados de um indexador de pesquisa cognitiva Azure. Consulte [a Connecting Azure SQL Database à Azure Cognitive Search utilizando os indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.

