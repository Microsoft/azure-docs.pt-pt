---
title: Segurança de dados e encriptação das melhores práticas - Microsoft Azure
description: Este artigo fornece um conjunto de boas práticas para segurança de dados e encriptação usando as capacidades do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: c88a2cf33cf1149a17af4a82dcf2858822f0c6d4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696154"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Melhores práticas de segurança e encriptação de dados do Azure
Este artigo descreve as melhores práticas para a segurança dos dados e encriptação.

As melhores práticas baseiam-se num consenso de opinião, e funcionam com as capacidades e conjuntos de funcionalidades atuais da plataforma Azure. As opiniões e as tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="protect-data"></a>Proteger os dados
Para ajudar a proteger os dados na nuvem, precisa de prestar contas aos possíveis estados em que os seus dados podem ocorrer e quais os controlos disponíveis para esse estado. As melhores práticas para a segurança e encriptação de dados da Azure dizem respeito aos seguintes estados de dados:

- Em repouso: Isto inclui todos os objetos de armazenamento de informação, recipientes e tipos que existem estáticamente em meios físicos, seja em disco magnético ou ótico.
- Em trânsito: Quando os dados são transferidos entre componentes, locais ou programas, está em trânsito. Exemplos são a transferência através da rede, através de um autocarro de serviço (de instalações para nuvem e vice-versa, incluindo ligações híbridas como o ExpressRoute), ou durante um processo de entrada/saída.

## <a name="choose-a-key-management-solution"></a>Escolha uma solução de gestão chave

Proteger as suas chaves é essencial para proteger os seus dados na nuvem.

O [Azure Key Vault](../../key-vault/general/overview.md) ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações na cloud e pelos serviços. O Key Vault simplifica o processo de gestão de chaves e permite-lhe manter o controlo das chaves que acedem e encriptam os seus dados. Os programadores podem criar as chaves de desenvolvimento e teste em minutos, e migrá-las para as chaves de produção. Os administradores de segurança podem conceder (e revogar) a permissão para as chaves, conforme necessário.

Pode usar o Cofre chave para criar vários recipientes seguros, chamados cofres. Estes cofres são apoiados por HSMs. Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Os cofres de chaves também controlam e registam o acesso a tudo o que está armazenado nos mesmos. O Azure Key Vault pode lidar com a solicitação e renovação dos certificados de Segurança da Camada de Transporte (TLS). Fornece funcionalidades para uma solução robusta para a gestão do ciclo de vida dos certificados.

O Azure Key Vault foi concebido para suportar chaves de aplicação e segredos. O Key Vault não se destina a ser uma loja para senhas de utilizador.

Seguem-se as melhores práticas de segurança para a utilização do Key Vault.

**Melhores práticas**: Conceder acesso a utilizadores, grupos e aplicações num âmbito específico.   
**Detalhe**: Utilize funções predefinidas Azure RBAC. Por exemplo, para conceder acesso a um utilizador para gerir cofres-chave, atribuiria o papel predefinido [Key Vault Contributor](../../role-based-access-control/built-in-roles.md) a este utilizador num âmbito específico. O âmbito neste caso seria uma subscrição, um grupo de recursos, ou apenas um cofre-chave específico. Se os papéis predefinidos não se adequarem às suas necessidades, pode [definir os seus próprios papéis](../../role-based-access-control/custom-roles.md).

**Melhores práticas**: Controle aquilo a que os utilizadores têm acesso.   
**Detalhe**: O acesso a um cofre chave é controlado através de duas interfaces distintas: plano de gestão e plano de dados. Os controlos de acesso do plano de gestão e do plano de dados funcionam de forma independente.

Use o Azure RBAC para controlar aquilo a que os utilizadores têm acesso. Por exemplo, se pretender conceder a uma aplicação acesso a chaves de utilização num cofre chave, basta conceder permissões de acesso a um plano de dados utilizando políticas de acesso ao cofre chave, e não é necessário qualquer acesso ao avião de gestão para esta aplicação. Inversamente, se pretender que um utilizador possa ler propriedades e etiquetas de cofre, mas não tiver qualquer acesso a chaves, segredos ou certificados, pode conceder a este utilizador acesso à leitura através do Azure RBAC, e não é necessário qualquer acesso ao plano de dados.

**Melhores práticas**: Guarde os certificados no seu cofre chave. Os seus certificados são de alto valor. Nas mãos erradas, a segurança da sua aplicação ou a segurança dos seus dados podem ser comprometidas.   
**Detalhe**: O Gestor de Recursos Azure pode implementar de forma segura certificados armazenados no Cofre da Chave Azure para VMs Azure quando os VMs são implantados. Ao definir políticas de acesso adequadas ao cofre de chaves, também pode controlar quem tem acesso ao seu certificado. Outra vantagem é poder gerir todos os seus certificados num único local no Azure Key Vault. Consulte [os Certificados de Implantação para VMs a partir do Cofre-Chave gerido pelo cliente](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault) para obter mais informações.

**Melhores práticas**: Certifique-se de que pode recuperar uma supressão de cofres-chave ou objetos chave do cofre.   
**Detalhe**: A eliminação de cofres-chave ou objetos chave do cofre pode ser inadvertida ou maliciosa. Ative as funcionalidades de eliminação de forma recuperável e proteção contra remoção do Key Vault, especialmente em chaves utilizadas para encriptar dados inativos. A eliminação destas chaves é equivalente à perda de dados, pelo que pode recuperar cofres eliminados e objetos de cofres se for necessário. Pratique operações de recuperação do Cofre chave regularmente.

> [!NOTE]
> Se um utilizador tiver permissões de contribuinte (Azure RBAC) para um avião de gestão de cofre chave, pode conceder-se acesso ao plano de dados, estabelecendo uma política de acesso ao cofre chave. Recomendamos que controle firmemente quem tem acesso ao seu colaborador aos seus cofres chave, para garantir que apenas as pessoas autorizadas podem aceder e gerir os seus cofres chave, chaves, segredos e certificados.
>
>

## <a name="manage-with-secure-workstations"></a>Gerir com estações de trabalho seguras

> [!NOTE]
> O administrador ou proprietário da subscrição deve utilizar uma estação de trabalho de acesso segura ou uma estação de trabalho de acesso privilegiada.
>
>

Como a grande maioria dos ataques visa o utilizador final, o ponto final torna-se um dos principais pontos de ataque. Um intruso que comprometa o ponto final pode usar as credenciais do utilizador para ter acesso aos dados da organização. A maioria dos ataques de pontos finais aproveitam-se do facto de os utilizadores serem administradores nos seus postos de trabalho locais.

**Melhores práticas**: Utilize uma estação de trabalho de gestão segura para proteger contas, tarefas e dados sensíveis.   
**Detalhe**: Utilize uma [estação de trabalho de acesso privilegiada](/windows-server/identity/securing-privileged-access/privileged-access-workstations) para reduzir a superfície de ataque em postos de trabalho. Estes postos de trabalho de gestão segura podem ajudá-lo a mitigar alguns destes ataques e garantir que os seus dados são mais seguros.

**Melhores práticas**: Garantir a proteção do ponto final.   
**Detalhe**: Impor políticas de segurança em todos os dispositivos que são utilizados para consumir dados, independentemente da localização dos dados (nuvem ou no local).

## <a name="protect-data-at-rest"></a>Proteger os dados inativos

[A encriptação de dados em repouso](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório para a privacidade dos dados, conformidade e soberania dos dados.

**Melhor prática**: Aplicar encriptação de disco para ajudar a salvaguardar os seus dados.   
**Detalhe**: Utilize [encriptação do disco Azure](./azure-disk-encryption-vms-vmss.md). Permite que os administradores de TI encriptem discos VM windows e Linux IaaS. A Encriptação do Disco combina a funcionalidade BitLocker do Windows padrão da indústria e a funcionalidade linux dm-crypt para fornecer encriptação de volume para o SISTEMA e os discos de dados.

Azure Storage e Azure SQL Database encriptam os dados em repouso por padrão, e muitos serviços oferecem encriptação como uma opção. Também pode utilizar o Azure Key Vault para manter o controlo das chaves que acedem e encriptam os dados. Consulte o suporte do modelo de [encriptação dos fornecedores de recursos Azure para saber mais.](encryption-atrest.md#azure-resource-providers-encryption-model-support)

**Melhores práticas**: Utilize encriptação para ajudar a mitigar riscos relacionados com o acesso não autorizado a dados.   
**Detalhe**: Criptografe as suas unidades antes de lhes escrever dados sensíveis.

As organizações que não aplicam a encriptação de dados estão mais expostas a questões de confidencialidade de dados. Por exemplo, utilizadores não autorizados ou fraudulentos podem roubar dados em contas comprometidas ou obter acesso não autorizado a dados codificados em Formato Claro. As empresas também devem provar que são diligentes e utilizam controlos de segurança corretos para reforçar a sua segurança de dados, a fim de cumprirem os regulamentos do setor.

## <a name="protect-data-in-transit"></a>Proteger os dados em trânsito

A proteção dos dados em trânsito deve ser uma parte essencial da estratégia de proteção de dados. Como os dados são movidos de um lado para o outro entre várias localizações, geralmente recomendamos que utilize sempre protocolos SSL/TLS para trocar dados entre diferentes localizações. Em algumas circunstâncias, talvez queira isolar todo o canal de comunicação entre as infraestruturas no local e na cloud com uma VPN.

Para mover dados entre a infraestrutura no local e o Azure, considere medidas de segurança apropriadas, como HTTPS ou VPN. Ao enviar tráfego encriptado entre uma rede virtual Azure e uma localização no local através da internet pública, utilize o [Azure VPN Gateway](../../vpn-gateway/index.yml).

Seguem-se as melhores práticas específicas da utilização do Gateway Azure VPN, SSL/TLS e HTTPS.

**Melhores práticas**: Acesso seguro de múltiplas estações de trabalho localizadas no local para uma rede virtual Azure.   
**Detalhe**: Utilize [a VPN local-a-local](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Melhores práticas**: Acesso seguro de uma estação de trabalho individual localizada no local para uma rede virtual Azure.   
**Detalhe**: Utilize [VPN ponto a local](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

**Melhores práticas**: Mova conjuntos de dados maiores sobre uma ligação WAN dedicada de alta velocidade.   
**Detalhe**: Use [ExpressRoute](../../expressroute/expressroute-introduction.md). Se optar por utilizar o ExpressRoute, também poderá encriptar os dados ao nível da aplicação com SSL/TLS ou outros protocolos para proteção adicional.

**Melhores práticas**: Interaja com o Azure Storage através do portal Azure.   
**Detalhe**: Todas as transações ocorrem através de HTTPS. Também pode utilizar [a API de armazenamento REST](/rest/api/storageservices/) sobre HTTPS para interagir com o [Azure Storage](https://azure.microsoft.com/services/storage/).

As organizações que não protegem os dados em trânsito são mais suscetíveis a [ataques man-in-the-middle](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), escutas e [sequestros](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))de sessão. Esses ataques podem ser o primeiro passo para obter acesso a dados confidenciais.

## <a name="secure-email-documents-and-sensitive-data"></a>E-mail seguro, documentos e dados sensíveis

Pretende controlar e proteger e-mails, documentos e dados sensíveis que partilhe fora da sua empresa. O [Azure Information Protection](/azure/information-protection/) é uma solução na cloud que ajuda uma organização a classificar, etiquetar e proteger os respetivos documentos e e-mails. Isto pode ser feito automaticamente por administradores que definem regras e condições, manualmente pelos utilizadores, ou uma combinação onde os utilizadores obtêm recomendações.

A classificação é sempre identificável, independentemente do local onde os dados são armazenados ou com quem são partilhados. As etiquetas incluem marcas visuais como cabeçalhos, rodapés ou marcas d'água. Os metadados são adicionados aos ficheiros e cabeçalhos de e-mail em texto não encriptado. O texto claro garante que outros serviços, como soluções para evitar a perda de dados, possam identificar a classificação e tomar as medidas adequadas.

A tecnologia de proteção utiliza a Azure Rights Management (Azure RMS). Esta tecnologia está integrada com outros serviços e aplicações na nuvem da Microsoft, como o Microsoft 365 e o Azure Ative Directory. Esta tecnologia de proteção utiliza políticas de autorização, encriptação e identidade. A proteção que é aplicada através do Azure RMS permanece com os documentos e e-mails, independentemente da localização — dentro ou fora da sua organização, redes, servidores de ficheiros e aplicações.

Esta solução de proteção de informação mantém-no no controlo dos seus dados, mesmo quando é partilhado com outras pessoas. Também pode utilizar o Azure RMS com as suas próprias aplicações de linha de negócio e soluções de proteção de informação de fornecedores de software, quer estas aplicações e soluções estejam no local ou na nuvem.

É recomendável que:

- [Implemente a Azure Information Protection](/azure/information-protection/deployment-roadmap) para a sua organização.
- Aplique rótulos que reflitam os requisitos do seu negócio. Por exemplo: Aplicar uma etiqueta chamada "altamente confidencial" a todos os documentos e e-mails que contenham dados ultrassecretos, para classificar e proteger estes dados. Em seguida, apenas os utilizadores autorizados podem aceder a estes dados, com quaisquer restrições que especifique.
- Configure o [registo de utilização para O Azure RMS](/azure/information-protection/log-analyze-usage) para que possa monitorizar a forma como a sua organização está a utilizar o serviço de proteção.

As organizações que são fracas na classificação de [dados](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e na proteção de ficheiros podem ser mais suscetíveis a fugas de dados ou uso indevido de dados. Com a proteção adequada de ficheiros, pode analisar fluxos de dados para obter informações sobre o seu negócio, detetar comportamentos de risco e tomar medidas corretivas, rastrear o acesso a documentos, e assim por diante.

## <a name="next-steps"></a>Passos seguintes

Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança da Azure para obter mais boas práticas de segurança quando estiver a desenhar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Estão disponíveis os seguintes recursos para fornecer informações mais gerais sobre a segurança da Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](/archive/blogs/azuresecurity/) - para informações atualizadas sobre as últimas informações em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail para secure@microsoft.com