---
title: Pesquisa do Azure de ligação de VM do SQL máquina virtual do Azure para a indexação da pesquisa-
description: Ativar ligações encriptadas e configurar a firewall para permitir ligações ao SQL Server numa máquina virtual do Azure (VM) a partir de um indexador no Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 90e5a133bac519cbc5ab2d7b112d51a019e8f698
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60871285"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar uma ligação a partir de um indexador de Azure Search para o SQL Server numa VM do Azure
Conforme observado na [ligar o Azure SQL Database para a Azure Search utilizando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), criação de indexadores contra **do SQL Server em VMs do Azure** (ou **VMs do SQL Azure** para abreviar) é suportada pelo Azure Search, mas há alguns pré-requisitos relacionados à segurança para cuidar do primeiro. 

Ligações a partir do Azure Search para o SQL Server numa VM é uma ligação de internet pública. Todas as medidas de segurança que pode normalmente seguir para que estas ligações se aplicam aqui também:

+ Obter um certificado de um [fornecedor de autoridade de certificação](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para o nome de domínio completamente qualificado da instância do SQL Server na VM do Azure.
+ Instalar o certificado na VM e, em seguida, ativar e configurar ligações encriptadas na VM com as instruções neste artigo.

## <a name="enable-encrypted-connections"></a>Ativar ligações encriptadas
O Azure Search necessita de um canal criptografado para todos os pedidos de indexador através de uma ligação de internet pública. Esta secção lista os passos para fazer isso funcionar.

1. Verifique as propriedades do certificado para verificar que o nome do requerente é o nome de domínio completamente qualificado (FQDN) da VM do Azure. Pode usar uma ferramenta como CertUtils ou o snap-in de certificados para ver as propriedades. Pode obter o FQDN da secção de Essentials do painel de serviço da VM, além da **etiqueta de nome DNS/endereço IP público** campo a [portal do Azure](https://portal.azure.com/).
   
   * Para as VMs criadas com a mais recente **Resource Manager** modelo, o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`
   * Para VMs anteriores criadas como um **clássica** VM, o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>`.

2. Configure o SQL Server para utilizar o certificado com o Editor de registo (regedit). 
   
    Embora o Gestor de configuração do SQL Server, muitas vezes, é utilizado para esta tarefa, não é possível usá-lo para este cenário. Ele não localizará o certificado importado porque o FQDN da VM no Azure não corresponde ao FQDN conforme determinado pela VM (identifica o domínio como o computador local ou o domínio de rede ao qual está associado). Quando os nomes não correspondem, use regedit para especificar o certificado.
   
   * Em regedit, navegue para esta chave de registo: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     O `[MSSQL13.MSSQLSERVER]` parte varia com base no nome da versão e instância. 
   * Defina o valor do **certificado** chave para o **thumbprint** do certificado SSL que importou para a VM.
     
     Existem várias formas de obter o thumbprint, algumas melhor do que outros. Se copiá-lo a partir da **certificados** snap-in no MMC, provavelmente selecionará um caráter de líderes do setor invisível [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta num erro ao tentar uma conexão . Existem várias soluções para corrigir este problema. O mais fácil é RETROCESSO sobre e, em seguida, volte a escrever o primeiro caráter de thumbprint para remover o caractere à esquerda no campo valor da chave no regedit. Em alternativa, pode utilizar uma ferramenta de diferente para copiar o thumbprint.

3. Conceder permissões para a conta de serviço. 
   
    Certifique-se de que a conta de serviço do SQL Server é concedida permissão apropriada na chave privada do certificado SSL. Se ignorar este passo, o SQL Server não será iniciada. Pode utilizar o **certificados** snap-in ou **CertUtils** para esta tarefa.
    
4. Reinicie o serviço do SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conectividade do SQL Server na VM
Depois de configurar a conexão criptografada necessário pelo Azure Search, existem passos de configuração adicionais intrínseco para o SQL Server em VMs do Azure. Se ainda não fez isso, a próxima etapa é concluir a configuração através de qualquer um dos seguintes artigos:

* Para uma **Resource Manager** VM, consulte [ligar a um SQL Server Máquina Virtual no Azure com o Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Para uma **clássica** VM, consulte [ligar a um SQL Server Máquina Virtual no Azure clássico](../virtual-machines/windows/classic/sql-connect.md).

Em particular, reveja a secção em cada artigo para "ligar através da internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurar o grupo de segurança de rede (NSG)
Não é incomum configurar o NSG e ponto final do Azure correspondente ou lista de controlo de acesso (ACL) para disponibilizar a sua VM do Azure a outras partes. É provável que já fizemos isso antes para permitir que a sua própria lógica de aplicação para ligar à sua VM do SQL Azure. Não é diferente para uma ligação de Azure Search à sua VM do SQL Azure. 

Os links a seguir fornecem instruções sobre configuração de NSG para implementações de VM. Utilize estas instruções para a ACL de um ponto de extremidade de Azure Search com base no respetivo endereço IP.

> [!NOTE]
> Para obter informações, veja [o que é um grupo de segurança de rede?](../virtual-network/security-overview.md)
> 
> 

* Para uma **Resource Manager** VM, consulte [como criar NSGs para implementações de ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Para uma **clássica** VM, consulte [como criar NSGs para as implementações clássicas](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Endereçamento IP pode apresentar alguns desafios que são facilmente superar se está atento o problema e possíveis soluções alternativas. As seções restantes fornecem recomendações para lidar com problemas relacionados com endereços IP na ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Restringir o acesso para o endereço IP do serviço de pesquisa
Recomendamos vivamente que restringir o acesso para o endereço IP do seu serviço de pesquisa na ACL em vez de fazer as suas VMs do SQL Azure totalmente aberto para quaisquer pedidos de ligação. Pode facilmente descobrir o endereço IP ao enviar pings para o FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) do seu serviço de pesquisa.

#### <a name="managing-ip-address-fluctuations"></a>Gerir a flutuações de endereço IP
Se o serviço de pesquisa tiver apenas uma unidade de pesquisa (ou seja, uma das réplicas e uma partição), o endereço IP será alterado durante a rotina de serviço reinícios, invalidando uma ACL existente com o endereço IP do seu serviço de pesquisa.

É uma forma de evitar o erro de conectividade subsequentes utilizar mais do que uma réplica e de uma partição no Azure Search. Se o fizer, aumenta o custo, mas que também resolve o problema de endereço IP. No Azure Search, os endereços IP não alterados quando tem mais do que uma unidade de pesquisa.

Uma segunda abordagem é permitir a conexão falhe e, em seguida, reconfigurar as ACLs no NSG. Em média, pode esperar endereços IP para alterar todas as semanas. Para os clientes que fazer a indexação controlada de forma pouco frequente, esta abordagem poderá ser viável.

Uma terceira abordagem viável (mas não particularmente segura) é especificar o intervalo de endereços IP da região do Azure onde o serviço de pesquisa está aprovisionado. A lista de intervalos de IP a partir do qual os endereços IP públicos são alocados a recursos do Azure está publicada em [intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Incluir os endereços IP de portais do Azure Search
Se estiver a utilizar o portal do Azure para criar um indexador, lógica de portal do Azure Search precisa também de aceder à sua VM do SQL Azure durante a hora de criação. Endereços IP de portais do Azure search podem ser encontrados ao enviar pings para `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Passos Seguintes
Com configuração fora do caminho, agora pode especificar um SQL Server numa VM do Azure como origem de dados para um indexador de Azure Search. Ver [ligar o Azure SQL Database para a Azure Search utilizando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.

