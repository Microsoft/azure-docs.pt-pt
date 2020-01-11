---
title: Conexão de VM do Azure SQL para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite conexões criptografadas e configure o firewall para permitir conexões a SQL Server em uma VM (máquina virtual) do Azure de um indexador no Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 12e642e59a1341926a0c4d66533465cecfc21709
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863143"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar uma conexão de um indexador Pesquisa Cognitiva do Azure para SQL Server em uma VM do Azure

Conforme observado na [conexão do banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores, a](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)criação de indexadores em relação a **SQL Server em VMs do azure** (ou **SQL Azure VMs** para curto) é suportada pelo Azure pesquisa cognitiva, mas há alguns pré-requisitos relacionados à segurança que devem ser considerados primeiro. 

As conexões do Pesquisa Cognitiva do Azure para SQL Server em uma VM são uma conexão de Internet pública. Todas as medidas de segurança que normalmente você seguiria para essas conexões também se aplicam aqui:

+ Obtenha um certificado de um [provedor de autoridade de certificação](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para o nome de domínio totalmente qualificado da instância de SQL Server na VM do Azure.
+ Instale o certificado na VM e, em seguida, habilite e configure as conexões criptografadas na VM usando as instruções neste artigo.

## <a name="enable-encrypted-connections"></a>Habilitar conexões criptografadas
O Pesquisa Cognitiva do Azure requer um canal criptografado para todas as solicitações do indexador em uma conexão de Internet pública. Esta seção lista as etapas para fazer esse trabalho.

1. Verifique as propriedades do certificado para verificar se o nome da entidade é o FQDN (nome de domínio totalmente qualificado) da VM do Azure. Você pode usar uma ferramenta como CertUtils ou o snap-in de certificados para exibir as propriedades. Você pode obter o FQDN da seção conceitos básicos da folha de serviço da VM, no campo **rótulo de endereço IP público/nome DNS** , na [portal do Azure](https://portal.azure.com/).
   
   * Para VMs criadas usando o modelo do **Resource Manager** mais recente, o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`
   * Para VMs mais antigas criadas como uma VM **clássica** , o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>`.

2. Configure SQL Server para usar o certificado usando o editor do registro (regedit). 
   
    Embora SQL Server Configuration Manager geralmente seja usado para essa tarefa, você não pode usá-la para esse cenário. Ele não encontrará o certificado importado porque o FQDN da VM no Azure não corresponde ao FQDN conforme determinado pela VM (ele identifica o domínio como o computador local ou o domínio de rede ao qual ele está associado). Quando os nomes não corresponderem, use regedit para especificar o certificado.
   
   * No regedit, navegue até esta chave do registro: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     A `[MSSQL13.MSSQLSERVER]` parte varia de acordo com a versão e o nome da instância. 
   * Defina o valor da chave de **certificado** para a **impressão digital** do certificado SSL que você importou para a VM.
     
     Há várias maneiras de obter a impressão digital, algumas melhores do que outras. Se você copiá-lo do snap-in de **certificados** no MMC, você provavelmente obterá um caractere de entrelinha invisível, [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta em um erro quando você tenta uma conexão. Existem várias soluções alternativas para corrigir esse problema. O mais fácil é fazer o Backspace e, em seguida, digitar novamente o primeiro caractere da impressão digital para remover o caractere à esquerda no campo valor da chave no regedit. Como alternativa, você pode usar uma ferramenta diferente para copiar a impressão digital.

3. Conceda permissões para a conta de serviço. 
   
    Verifique se a conta de serviço SQL Server recebeu a permissão apropriada na chave privada do certificado SSL. Se você ignorar esta etapa, SQL Server não será iniciada. Você pode usar o snap-in de **certificados** ou **certutils** para esta tarefa.
    
4. Reinicie o serviço do SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conectividade de SQL Server na VM
Depois de configurar a conexão criptografada exigida pelo Azure Pesquisa Cognitiva, há etapas de configuração adicionais intrínsecas a SQL Server em VMs do Azure. Se você ainda não fez isso, a próxima etapa é concluir a configuração usando um destes artigos:

* Para uma VM do **Resource Manager** , consulte [conectar-se a uma máquina virtual SQL Server no Azure usando o Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Para uma VM **clássica** , consulte [conectar-se a uma máquina virtual SQL Server no Azure clássico](../virtual-machines/windows/classic/sql-connect.md).

Em particular, examine a seção em cada artigo para "conectando-se pela Internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurar o grupo de segurança de rede (NSG)
Não é incomum configurar o NSG e o ponto de extremidade do Azure correspondente ou a ACL (lista de controle de acesso) para tornar sua VM do Azure acessível a outras partes. É provável que você tenha feito isso antes de permitir que sua própria lógica de aplicativo se conecte à VM SQL Azure. Não é diferente para uma conexão de Pesquisa Cognitiva do Azure com sua VM SQL Azure. 

Os links a seguir fornecem instruções sobre a configuração do NSG para implantações de VM. Use estas instruções para obter uma ACL de um ponto de extremidade de Pesquisa Cognitiva do Azure com base em seu endereço IP.

> [!NOTE]
> Para obter informações, consulte [o que é um grupo de segurança de rede?](../virtual-network/security-overview.md)
> 
> 

* Para uma VM do **Resource Manager** , consulte [como criar NSGs para implantações do ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Para uma VM **clássica** , consulte [como criar NSGs para implantações clássicas](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

O endereçamento IP pode apresentar alguns desafios que serão facilmente solucionados se você estiver atento ao problema e possíveis soluções alternativas. As seções restantes fornecem recomendações para lidar com problemas relacionados a endereços IP na ACL.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Restringir o acesso ao Pesquisa Cognitiva do Azure
É altamente recomendável que você restrinja o acesso ao endereço IP do serviço de pesquisa e o intervalo de endereços IP da [marca de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) `AzureCognitiveSearch` na ACL em vez de tornar suas VMs SQL Azure abertas para todas as solicitações de conexão.

Você pode descobrir o endereço IP executando ping no FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) de seu serviço de pesquisa.

Você pode descobrir o intervalo de endereços IP de `AzureCognitiveSearch` [marca de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) para a região específica em que o serviço de pesquisa cognitiva do Azure está localizado usando [arquivos JSON baixáveis](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) ou por meio da [API de descoberta de marca de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). O intervalo de endereços IP é atualizado semanalmente.

#### <a name="managing-ip-address-fluctuations"></a>Gerenciando flutuações de endereço IP
Se o serviço de pesquisa tiver apenas uma unidade de pesquisa (ou seja, uma réplica e uma partição), o endereço IP será alterado durante a reinicialização do serviço de rotina, invalidando uma ACL existente com o endereço IP do serviço de pesquisa.

Uma maneira de evitar o erro de conectividade subsequente é usar mais de uma réplica e uma partição no Azure Pesquisa Cognitiva. Isso aumenta o custo, mas também resolve o problema de endereço IP. No Azure Pesquisa Cognitiva, os endereços IP não são alterados quando você tem mais de uma unidade de pesquisa.

Uma segunda abordagem é permitir que a conexão falhe e, em seguida, reconfigurar as ACLs no NSG. Em média, você pode esperar que os endereços IP sejam alterados em intervalos de semanas. Para clientes que controlam a indexação com pouca frequência, essa abordagem pode ser viável.

Uma terceira abordagem viável (mas não particularmente segura) é especificar o intervalo de endereços IP da região do Azure em que o serviço de pesquisa é provisionado. A lista de intervalos de IP de onde os endereços IP públicos são alocados aos recursos do Azure é publicada em [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Incluir os endereços IP do portal de Pesquisa Cognitiva do Azure
Se você estiver usando o portal do Azure para criar um indexador, a lógica do portal de Pesquisa Cognitiva do Azure também precisará acessar a VM do SQL Azure durante a criação. Os endereços IP do portal de Pesquisa Cognitiva do Azure podem ser encontrados por meio do ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Passos seguintes
Com a configuração fora do caminho, agora você pode especificar um SQL Server na VM do Azure como a fonte de dados para um indexador de Pesquisa Cognitiva do Azure. Consulte [conectando o banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.

