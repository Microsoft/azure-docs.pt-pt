---
title: Segurança de dados e encriptação boas práticas - Microsoft Azure
description: Este artigo fornece um conjunto de boas práticas para a segurança de dados e encriptação usando as capacidades do Azure.
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
ms.openlocfilehash: c5bf62f434b2095f7200b5562c38c252a0195c5b
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945915"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Melhores práticas de segurança e encriptação de dados do Azure
Este artigo descreve as melhores práticas para a segurança de dados e encriptação.

As melhores práticas baseiam-se num consenso de opinião, e funcionam com as capacidades atuais da plataforma Azure e conjuntos de funcionalidades. As opiniões e as tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="protect-data"></a>Proteger os dados
Para ajudar a proteger os dados na nuvem, é necessário prestar contas aos possíveis estados em que os seus dados podem ocorrer e quais os controlos disponíveis para esse estado. As melhores práticas para a segurança e encriptação de dados do Azure dizem respeito aos seguintes estados de dados:

- Em repouso: Isto inclui todos os objetos de armazenamento de informação, recipientes e tipos que existem estáticamente nos meios físicos, seja em disco magnético ou ótico.
- Em trânsito: Quando os dados estão a ser transferidos entre componentes, locais ou programas, está em trânsito. Exemplos são transferências através da rede, através de um ônibus de serviço (de instalações para nuvem e vice-versa, incluindo ligações híbridas como ExpressRoute), ou durante um processo de entrada/saída.

## <a name="choose-a-key-management-solution"></a>Escolha uma solução de gestão chave

Proteger as chaves é essencial para proteger os seus dados na nuvem.

[O Azure Key Vault](/azure/key-vault/key-vault-overview) ajuda a salvaguardar chaves e segredos criptográficos que as aplicações e serviços em nuvem usam. A chave de cofre simplifica o processo de gestão de chaves e permite-lhe manter o controlo de teclas que acede e encripta os seus dados. Os desenvolvedores podem criar chaves para desenvolvimento e teste em minutos, e depois migrar para chaves de produção. Os administradores de segurança podem conceder (e revogar) a permissão para as chaves, conforme necessário.

Pode usar o Cofre chave para criar vários recipientes seguros, chamados cofres. Estes cofres são apoiados por HSMs. Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Os cofres-chave também controlam e registam o acesso a tudo o que lhes é armazenado. O Cofre de Chaves Azure pode lidar com a solicitação e renovação dos certificados de Segurança da Camada de Transporte (TLS). Fornece funcionalidades para uma solução robusta para a gestão do ciclo de vida dos certificados.

O Azure Key Vault foi concebido para apoiar chaves e segredos de aplicação. O Key Vault não se destina a ser uma loja para senhas de utilizador.

Seguem-se as melhores práticas de segurança para o uso do Cofre chave.

**Boas práticas**: Conceder acesso a utilizadores, grupos e aplicações num âmbito específico.   
**Detalhe**: Utilize as funções predefinidas do RBAC. Por exemplo, para conceder acesso a um utilizador para gerir cofres chave, você atribuiria a função predefinida [Key Vault Contributor](/azure/role-based-access-control/built-in-roles) a este utilizador num âmbito específico. O âmbito neste caso seria uma subscrição, um grupo de recursos, ou apenas um cofre específico. Se os papéis predefinidos não se adequarem às suas necessidades, pode [definir os seus próprios papéis.](/azure/role-based-access-control/custom-roles)

**Boas práticas**: Controlar o que os utilizadores têm acesso.   
**Detalhe**: O acesso a um cofre chave é controlado através de duas interfaces separadas: plano de gestão e plano de dados. Os controlos de acesso do plano de gestão e do plano de dados funcionam de forma independente.

Utilize o RBAC para controlar o que os utilizadores têm acesso. Por exemplo, se quiser conceder acesso a uma aplicação para usar chaves num cofre chave, apenas precisa de conceder permissões de acesso a planos de dados utilizando políticas de acesso ao cofre chave, e não é necessário acesso a planos de gestão para esta aplicação. Inversamente, se quiser que um utilizador possa ler propriedades e etiquetas do cofre, mas não tiver acesso a chaves, segredos ou certificados, pode conceder a este utilizador o acesso de leitura através do RBAC, e não é necessário acesso ao plano de dados.

**Boas práticas**: Guarde os certificados no seu cofre chave. Seus certificados são de alto valor. Nas mãos erradas, a segurança da sua aplicação ou a segurança dos seus dados podem ser comprometidas.   
**Detalhe**: O Gestor de Recursos Azure pode implementar de forma segura os certificados armazenados no Cofre de Chaves Azure para VMs Azure quando os VMs forem implantados. Ao definir as políticas de acesso adequadas para o cofre chave, também controla quem tem acesso ao seu certificado. Outro benefício é gerir todos os seus certificados num só lugar no Cofre chave Azure. Consulte [os Certificados de Implementação para VMs a partir do Key Vault gerido pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) para obter mais informações.

**Boas práticas**: Certifique-se de que pode recuperar uma supressão de cofres chave ou objetos de cofre chave.   
**Detalhe**: A eliminação de cofres chave ou objetos de cofre chave pode ser inadvertidaou ou maliciosa. Ative as funcionalidades de eliminação suave e de proteção de purga do Key Vault, especialmente para as teclas que são utilizadas para encriptar dados em repouso. A eliminação destas chaves equivale à perda de dados, para que possa recuperar cofres apagados e objetos de cofre, se necessário. Pratique as operações de recuperação do Cofre Chave regularmente.

> [!NOTE]
> Se um utilizador tiver permissões contributivas (RBAC) para um plano de gestão de cofres chave, pode conceder-se acesso ao plano de dados definindo uma política de acesso ao cofre chave. Recomendamos que controle rigorosamente quem tem acesso aos seus cofres chave, para garantir que apenas pessoas autorizadas possam aceder e gerir os seus cofres chave, chaves, segredos e certificados.
>
>

## <a name="manage-with-secure-workstations"></a>Gerir com estações de trabalho seguras

> [!NOTE]
> O administrador ou proprietário de subscrição deve utilizar uma estação de trabalho de acesso seguro ou uma estação de trabalho privilegiada de acesso.
>
>

Como a grande maioria dos ataques tem como alvo o utilizador final, o ponto final torna-se um dos principais pontos de ataque. Um intruso que comprometa o ponto final pode usar as credenciais do utilizador para ter acesso aos dados da organização. A maioria dos ataques de ponto final aproveitam o facto de os utilizadores serem administradores nas suas estações de trabalho locais.

**Boas práticas**: Utilize uma estação de trabalho de gestão segura para proteger contas, tarefas e dados sensíveis.   
**Detalhe**: Utilize uma [estação](https://technet.microsoft.com/library/mt634654.aspx) de trabalho privilegiada para reduzir a superfície de ataque em estações de trabalho. Estes postos de trabalho de gestão seguros podem ajudá-lo a mitigar alguns destes ataques e garantir que os seus dados são mais seguros.

**Boas práticas**: Assegurar a proteção do ponto final.   
**Detalhe**: Impor políticas de segurança em todos os dispositivos utilizados para consumir dados, independentemente da localização dos dados (cloud ou on-local).

## <a name="protect-data-at-rest"></a>Proteja os dados em repouso

[A encriptação](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) de dados em repouso é um passo obrigatório para a privacidade dos dados, conformidade e soberania de dados.

**Boas práticas**: Aplique encriptação de disco para ajudar a salvaguardar os seus dados.   
**Detalhe**: Utilize encriptação de [disco azure](/azure/security/azure-security-disk-encryption-overview). Permite que os administradores de TI criptografem discos VM Windows e Linux IaaS. A Encriptação do Disco combina a funcionalidade De Sigrafe Padrão da indústria e a funcionalidade de dm-criptolin para fornecer encriptação de volume para o SISTEMA e os discos de dados.

O Azure Storage e o Azure SQL Database encriptam os dados em repouso por padrão, e muitos serviços oferecem encriptação como uma opção. Pode utilizar o Cofre de Chaves Azure para manter o controlo das teclas que acedem e encriptam os seus dados. Consulte o suporte do modelo de encriptação dos fornecedores de [recursos Azure para saber mais](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Boas práticas**: Utilize a encriptação para ajudar a mitigar os riscos relacionados com o acesso não autorizado a dados.   
**Detalhe**: Criptografe os seus discos antes de escrever dados sensíveis.

Organizações que não aplicam encriptação de dados estão mais expostas a problemas de confidencialidade de dados. Por exemplo, utilizadores não autorizados ou fraudulentos podem roubar dados em contas comprometidas ou obter acesso não autorizado a dados codificados em Formato Claro. As empresas também devem provar que são diligentes e utilizam controlos de segurança corretos para reforçar a sua segurança de dados, a fim de cumprir em conformidade com os regulamentos do setor.

## <a name="protect-data-in-transit"></a>Proteger dados em trânsito

Proteger os dados em trânsito deve ser uma parte essencial da sua estratégia de proteção de dados. Como os dados estão a mover-se de vários locais, recomendamos geralmente que utilize sempre protocolos SSL/TLS para trocar dados em diferentes locais. Em algumas circunstâncias, você pode querer isolar todo o canal de comunicação entre as suas instalações e infraestruturas em nuvem usando uma VPN.

Para obter dados que se desloquem entre a sua infraestrutura no local e o Azure, considere as salvaguardas adequadas, tais como HTTPS ou VPN. Ao enviar tráfego encriptado entre uma rede virtual Azure e uma localização no local através da internet pública, utilize [o Azure VPN Gateway](../../vpn-gateway/index.yml).

Seguem-se as melhores práticas específicas para a utilização do Azure VPN Gateway, SSL/TLS e HTTPS.

**Boas práticas**: Acesso seguro de várias estações de trabalho localizadas no local para uma rede virtual Azure.   
**Detalhe**: Utilize [VPN site-to-site](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Boas práticas**: Acesso seguro de uma estação de trabalho individual localizada no local para uma rede virtual Azure.   
**Detalhe**: Utilize [VPN ponto-a-local](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Boas práticas**: Mova conjuntos de dados maiores sobre uma ligação WAN dedicada de alta velocidade.   
**Detalhe**: Utilize a [Via Expressa](/azure/expressroute/expressroute-introduction). Se optar por utilizar o ExpressRoute, também pode encriptar os dados ao nível da aplicação utilizando SSL/TLS ou outros protocolos para uma maior proteção.

**Boas práticas**: Interaja com o Armazenamento Azure através do portal Azure.   
**Detalhe**: Todas as transações ocorrem via HTTPS. Também pode utilizar [a Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) em HTTPS para interagir com [o Armazenamento Azure.](https://azure.microsoft.com/services/storage/)

As organizações que não protegem os dados em trânsito são mais suscetíveis a [ataques entre homens](https://technet.microsoft.com/library/gg195821.aspx)e médios, [escutas](https://technet.microsoft.com/library/gg195641.aspx)e sequestros de sessões. Estes ataques podem ser o primeiro passo para obter acesso a dados confidenciais.

## <a name="secure-email-documents-and-sensitive-data"></a>Email seguro, documentos e dados sensíveis

Deseja controlar e proteger e-mails, documentos e dados sensíveis que partilha fora da sua empresa. [A Azure Information Protection](/azure/information-protection/) é uma solução baseada na nuvem que ajuda uma organização a classificar, rotular e proteger os seus documentos e e-mails. Isto pode ser feito automaticamente por administradores que definem regras e condições, manualmente pelos utilizadores, ou uma combinação onde os utilizadores recebem recomendações.

A classificação é identificável em todos os momentos, independentemente do local onde os dados são armazenados ou com quem são partilhados. As etiquetas incluem marcas visuais como cabeçalho, rodapé ou marca de água. Os metadados são adicionados a ficheiros e cabeçalhos de e-mail em texto claro. O texto claro garante que outros serviços, tais como soluções para evitar a perda de dados, podem identificar a classificação e tomar as medidas adequadas.

A tecnologia de proteção utiliza a Azure Rights Management (Azure RMS). Esta tecnologia está integrada com outros serviços e aplicações na nuvem da Microsoft, como o Office 365 e o Azure Ative Directory. Esta tecnologia de proteção utiliza políticas de encriptação, identidade e autorização. A proteção que é aplicada através do Azure RMS fica com os documentos e e-mails, independentemente da localização — dentro ou fora da sua organização, redes, servidores de ficheiros e aplicações.

Esta solução de proteção de informação mantém-no no controlo dos seus dados, mesmo quando é partilhado com outras pessoas. Também pode utilizar o Azure RMS com as suas próprias aplicações de linha de negócio e soluções de proteção de informação de fornecedores de software, quer estas aplicações e soluções estejam no local ou na nuvem.

É recomendável que:

- [Implemente a Proteção de Informação Azure](/azure/information-protection/deployment-roadmap) para a sua organização.
- Aplique etiquetas que reflitam os seus requisitos de negócio. Por exemplo: Aplique uma etiqueta chamada "altamente confidencial" em todos os documentos e e-mails que contenham dados ultrassecretos, para classificar e proteger estes dados. Em seguida, apenas os utilizadores autorizados podem aceder a estes dados, com quaisquer restrições que especifique.
- Configure o [registo de utilização para O RMS Azure](/azure/information-protection/log-analyze-usage) para que possa monitorizar a forma como a sua organização está a utilizar o serviço de proteção.

As organizações que são fracas na [classificação](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) de dados e na proteção de ficheiros podem ser mais suscetíveis a fugas de dados ou utilização indevida de dados. Com a proteção adequada dos ficheiros, pode analisar fluxos de dados para obter informações sobre o seu negócio, detetar comportamentos de risco e tomar medidas corretivas, rastrear o acesso aos documentos, e assim por diante.

## <a name="next-steps"></a>Passos seguintes

Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança azure para obter mais práticas de segurança para usar quando está a projetar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais sobre segurança Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - para informações atualizadas sobre as últimas novidades em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail para secure@microsoft.com
