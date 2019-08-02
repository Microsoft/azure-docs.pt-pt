---
title: Práticas recomendadas de segurança de dados e criptografia-Microsoft Azure
description: Este artigo fornece um conjunto de práticas recomendadas para segurança de dados e criptografia usando recursos internos do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 4df024f9ff37121df4d9be51ae1fa540028ddd3d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727421"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Melhores práticas de segurança e encriptação de dados do Azure
Este artigo descreve as práticas recomendadas para segurança e criptografia de dados.

As práticas recomendadas baseiam-se em um consenso de opinião e funcionam com os recursos atuais da plataforma Azure e os conjuntos de recursos. As opiniões e tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="protect-data"></a>Proteger os dados
Para ajudar a proteger os dados na nuvem, você precisa considerar os possíveis Estados em que os dados podem ocorrer e quais controles estão disponíveis para esse estado. As práticas recomendadas para segurança e criptografia de dados do Azure estão relacionadas aos seguintes Estados de dados:

- Em repouso: Isso inclui todos os objetos de armazenamento de informações, contêineres e tipos que existem estaticamente em mídia física, seja em disco magnético ou óptico.
- Em trânsito: Quando os dados estão sendo transferidos entre componentes, locais ou programas, eles estão em trânsito. Os exemplos são transferidos pela rede, em um barramento de serviço (do local para a nuvem e vice-versa, incluindo conexões híbridas, como ExpressRoute), ou durante um processo de entrada/saída.

## <a name="choose-a-key-management-solution"></a>Escolher uma solução de gerenciamento de chaves

Proteger suas chaves é essencial para proteger seus dados na nuvem.

[Azure Key Vault](/azure/key-vault/key-vault-overview) ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços de nuvem. A chave de cofre simplifica o processo de gestão de chaves e permite-lhe manter o controlo de teclas que acede e encripta os seus dados. Os desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e, em seguida, migrá-las para chaves de produção. Os administradores de segurança podem conceder (e revogar) a permissão para as chaves, conforme necessário.

Você pode usar Key Vault para criar vários contêineres seguros, chamados cofres. Esses cofres são apoiados por HSMs. Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Os cofres de chaves também controlam e registram o acesso a qualquer coisa armazenada neles. Azure Key Vault pode lidar com a solicitação e a renovação de certificados de TLS (segurança da camada de transporte). Ele fornece recursos para uma solução robusta para o gerenciamento do ciclo de vida de certificados.

O Azure Key Vault foi projetado para dar suporte a chaves de aplicativo e segredos. O Key Vault não se destina a ser um repositório para senhas de usuário.

A seguir estão as práticas recomendadas de segurança para usar Key Vault.

**Prática recomendada**: Conceder acesso a usuários, grupos e aplicativos em um escopo específico.   
**Detalhe**: Use as funções predefinidas do RBAC. Por exemplo, para conceder acesso a um usuário para gerenciar cofres de chaves, você atribuiria a função predefinida [Key Vault colaborador](/azure/role-based-access-control/built-in-roles) a esse usuário em um escopo específico. Nesse caso, o escopo seria uma assinatura, um grupo de recursos ou apenas um cofre de chaves específico. Se as funções predefinidas não atenderem às suas necessidades, você poderá [definir suas próprias funções](/azure/role-based-access-control/custom-roles).

**Prática recomendada**: Controle o que os usuários têm acesso.   
**Detalhe**: O controlo aos cofres de chaves é controlado através de duas interfaces separadas - o plano de gestão e o plano de dados. Os controlos de acesso do plano de gestão e do plano de dados funcionam de forma independente.

Use o RBAC para controlar o que os usuários têm acesso. Por exemplo, se você quiser conceder a um aplicativo acesso para usar chaves em um cofre de chaves, você só precisa conceder permissões de acesso do plano de dados usando políticas de acesso do Key Vault e nenhum acesso ao plano de gerenciamento é necessário para esse aplicativo. Por outro lado, se você quiser que um usuário possa ler as propriedades e marcas do cofre, mas não tiver acesso a chaves, segredos ou certificados, poderá conceder a esse usuário acesso de leitura usando o RBAC e nenhum acesso ao plano de dados será necessário.

**Prática recomendada**: Armazene certificados em seu cofre de chaves. Seus certificados são de valor alto. Em mãos erradas, a segurança de seu aplicativo ou a segurança de seus dados pode ser comprometida.   
**Detalhe**: Azure Resource Manager pode implantar com segurança certificados armazenados em Azure Key Vault para VMs do Azure quando as VMs são implantadas. Ao definir políticas de acesso apropriadas para o cofre de chaves, você também controla quem obtém acesso ao seu certificado. Outro benefício é que você gerencia todos os seus certificados em um único lugar no Azure Key Vault. Consulte [implantar certificados em VMs de Key Vault gerenciados pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) para obter mais informações.

**Prática recomendada**: Verifique se você pode recuperar uma exclusão de cofres de chaves ou objetos do Key Vault.   
**Detalhe**: A exclusão de cofres de chaves ou objetos do cofre de chaves pode ser inadvertida ou mal-intencionada. Habilite os recursos de proteção de exclusão reversível e limpeza do Key Vault, especialmente para chaves que são usadas para criptografar dados em repouso. A exclusão dessas chaves é equivalente à perda de dados, para que você possa recuperar cofres e objetos de cofre excluídos, se necessário. Pratique Key Vault operações de recuperação regularmente.

> [!NOTE]
> Se um usuário tiver as permissões de colaborador (RBAC) para um plano de gerenciamento do cofre de chaves, ele poderá conceder acesso ao plano de dados, definindo uma política de acesso do cofre de chaves. É recomendável que você controle rigidamente quem tem acesso de colaborador para seus cofres de chaves, para garantir que apenas pessoas autorizadas possam acessar e gerenciar cofres de chaves, chaves, segredos e certificados.
>
>

## <a name="manage-with-secure-workstations"></a>Gerenciar com estações de trabalho seguras

> [!NOTE]
> O administrador ou o proprietário da assinatura deve usar uma estação de trabalho de acesso seguro ou uma estação de trabalho com acesso privilegiado.
>
>

Como a grande maioria dos ataques visam o usuário final, o ponto de extremidade se torna um dos principais pontos de ataque. Um invasor que compromete o ponto de extremidade pode usar as credenciais do usuário para obter acesso aos dados da organização. A maioria dos ataques de ponto de extremidade aproveita o fato de que os usuários são administradores em suas estações de trabalho locais.

**Prática recomendada**: Use uma estação de trabalho de gerenciamento seguro para proteger contas, tarefas e dados confidenciais.   
**Detalhe**: Use uma [estação de trabalho com acesso privilegiado](https://technet.microsoft.com/library/mt634654.aspx) para reduzir a superfície de ataque em estações de trabalho. Essas estações de trabalho de gerenciamento seguro podem ajudá-lo a reduzir alguns desses ataques e garantir que seus dados sejam mais seguros.

**Prática recomendada**: Garanta o Endpoint Protection.   
**Detalhe**: Impor políticas de segurança em todos os dispositivos que são usados para consumir dados, independentemente do local de dados (nuvem ou local).

## <a name="protect-data-at-rest"></a>Proteger dados em repouso

[A criptografia de dados em repouso](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória para a privacidade de dados, a conformidade e a soberania de dados.

**Prática recomendada**: Aplique a criptografia de disco para ajudar a proteger seus dados.   
**Detalhe**: Use [Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview). Ele permite que os administradores de ti criptografem os discos de VM IaaS Windows e Linux. A criptografia de disco combina o recurso Windows BitLocker padrão do setor e o recurso DM-cript do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados.

O armazenamento do Azure e o banco de dados SQL do Azure criptografam em repouso por padrão e muitos serviços oferecem criptografia como uma opção. Você pode usar Azure Key Vault para manter o controle de chaves que acessam e criptografam seus dados. Consulte [suporte ao modelo de criptografia dos provedores de recursos do Azure para saber mais](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Práticas recomendadas**: Use a criptografia para ajudar a reduzir os riscos relacionados ao acesso a dados não autorizado.   
**Detalhe**: Criptografe suas unidades antes de gravar dados confidenciais neles.

As organizações que não impõem criptografia de dados são mais expostas a problemas de confidencialidade de dados. Por exemplo, usuários não autorizados ou não autorizados podem roubar dados em contas comprometidas ou obter acesso não autorizado a dados codificados em formato limpo. As empresas também devem provar que são uma prova e usando os controles de segurança corretos para aprimorar a segurança dos dados a fim de atender às normas do setor.

## <a name="protect-data-in-transit"></a>Proteger dados em trânsito

Proteger os dados em trânsito deve ser uma parte essencial da sua estratégia de proteção de dados. Como os dados são movidos para frente e para trás de vários locais, geralmente recomendamos que você sempre use protocolos SSL/TLS para trocar dados entre diferentes locais. Em algumas circunstâncias, talvez você queira isolar todo o canal de comunicação entre suas infraestruturas locais e na nuvem usando uma VPN.

Para a movimentação de dados entre sua infraestrutura local e o Azure, considere as proteções apropriadas, como HTTPS ou VPN. Ao enviar tráfego criptografado entre uma rede virtual do Azure e uma localização local pela Internet pública, use o [Gateway de VPN do Azure](https://docs.microsoft.com/azure/vpn-gateway/).

A seguir estão as práticas recomendadas específicas para usar o gateway de VPN do Azure, SSL/TLS e HTTPS.

**Prática recomendada**: Acesso seguro de várias estações de trabalho localizadas localmente para uma rede virtual do Azure.   
**Detalhe**: Use [VPN site a site](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Prática recomendada**: Acesso seguro de uma estação de trabalho individual localizada localmente para uma rede virtual do Azure.   
**Detalhe**: Use [VPN ponto a site](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Prática recomendada**: Mova conjuntos de dados maiores em um link WAN dedicado de alta velocidade.   
**Detalhe**: Use o [ExpressRoute](/azure/expressroute/expressroute-introduction). Se você optar por usar o ExpressRoute, também poderá criptografar os dados no nível do aplicativo usando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para proteção adicional.

**Prática recomendada**: Interaja com o armazenamento do Azure por meio do portal do Azure.   
**Detalhe**: Todas as transações ocorrem via HTTPS. Você também pode usar a [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd179355.aspx) sobre HTTPS para interagir com o [armazenamento do Azure](https://azure.microsoft.com/services/storage/).

As organizações que não protegem os dados em trânsito são mais suscetíveis a [ataques man-in-the-Middle](https://technet.microsoft.com/library/gg195821.aspx), [espionagem](https://technet.microsoft.com/library/gg195641.aspx)e seqüestro de sessão. Esses ataques podem ser a primeira etapa para obter acesso a dados confidenciais.

## <a name="secure-email-documents-and-sensitive-data"></a>Proteger emails, documentos e dados confidenciais

Você deseja controlar e proteger emails, documentos e dados confidenciais que você compartilha fora de sua empresa. A [proteção de informações do Azure](/azure/information-protection/) é uma solução baseada em nuvem que ajuda uma organização a classificar, rotular e proteger seus documentos e emails. Isso pode ser feito automaticamente por administradores que definem regras e condições, manualmente por usuários ou uma combinação onde os usuários obtêm recomendações.

A classificação é identificável o tempo todo, independentemente de onde os dados são armazenados ou com quem eles são compartilhados. Os rótulos incluem marcações visuais, como um cabeçalho, rodapé ou marca-d ' água. Os metadados são adicionados aos arquivos e cabeçalhos de email em texto não criptografado. O texto não criptografado garante que outros serviços, como soluções para evitar a perda de dados, possam identificar a classificação e tomar as devidas medidas.

A tecnologia de proteção usa o Azure Rights Management (Azure RMS). Essa tecnologia é integrada a outros serviços e aplicativos de nuvem da Microsoft, como o Office 365 e Azure Active Directory. Essa tecnologia de proteção usa políticas de criptografia, identidade e autorização. A proteção aplicada por meio do Azure RMS permanece com os documentos e emails, independentemente do local — dentro ou fora de sua organização, redes, servidores de arquivos e aplicativos.

Essa solução de proteção de informações mantém você no controle de seus dados, mesmo quando eles são compartilhados com outras pessoas. Você também pode usar Azure RMS com seus próprios aplicativos de linha de negócios e soluções de proteção de informações de fornecedores de software, independentemente de esses aplicativos e soluções estarem no local ou na nuvem.

É recomendável que você:

- [Implante a proteção de informações do Azure](/azure/information-protection/deployment-roadmap) para sua organização.
- Aplique rótulos que reflitam seus requisitos de negócios. Por exemplo: Aplique um rótulo chamado "altamente confidencial" a todos os documentos e emails que contenham dados de segredo superior, para classificar e proteger esses dados. Em seguida, somente os usuários autorizados podem acessar esses dados, com as restrições que você especificar.
- Configure o [log de uso para Azure RMS](/azure/information-protection/log-analyze-usage) para que você possa monitorar como sua organização está usando o serviço de proteção.

As organizações que são fracas na [classificação de dados](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e na proteção de arquivos podem ser mais suscetíveis à perda de dados ou ao uso indevido de dados. Com a proteção adequada de arquivos, você pode analisar fluxos de dados para obter informações sobre seus negócios, detectar comportamentos arriscados e tomar medidas corretivas, controlar o acesso a documentos e assim por diante.

## <a name="next-steps"></a>Passos Seguintes

Veja [padrões e práticas recomendadas de segurança do Azure](best-practices-and-patterns.md) para obter mais práticas recomendadas de segurança para usar ao projetar, implantar e gerenciar suas soluções de nuvem usando o Azure.

Os recursos a seguir estão disponíveis para fornecer informações mais gerais sobre segurança do Azure e serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre o mais recente na segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou enviadas por email parasecure@microsoft.com
