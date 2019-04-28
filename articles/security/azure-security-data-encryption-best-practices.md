---
title: Melhores práticas de segurança de dados e de encriptação | Documentos da Microsoft
description: Este artigo fornece um conjunto de práticas recomendadas para segurança de dados e recursos do Azure através de encriptação internos.
services: security
documentationcenter: na
author: barclayn
manager: mbalwin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: barclayn
ms.openlocfilehash: 686d4a8ac5239af12206b57072cc00aa10114d79
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125124"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Práticas recomendadas de segurança de dados do Azure e encriptação

Para ajudar a proteger os dados na cloud, precisa levar em conta para os Estados possíveis em que os dados podem ocorrer e quais controles estão disponíveis para esse Estado. Melhores práticas para segurança de dados do Azure e a encriptação se relacionam com os seguintes Estados de dados:

- Inativos: Isto inclui todos os objetos de armazenamento de informações, contentores e tipos existentes estaticamente em mídia física, se magnético ou ótico disco.
- Em trânsito: Quando os dados estão a ser transferidos entre componentes, locais ou programas, se encontram em trânsito. Os exemplos são transferência através da rede, num barramento de serviço (a partir de locais para a cloud e vice-versa, incluindo as ligações híbridas, como o ExpressRoute), ou durante um processo de entrada/saída.

Neste artigo, abordaremos uma coleção de práticas de melhor segurança e encriptação de dados do Azure. Essas práticas recomendadas são derivadas da nossa experiência com a segurança de dados do Azure e a encriptação e as experiências dos clientes, como.

Para cada melhor prática, vamos explicar:

* O que é a melhor prática
* Por que pretende ativar essa prática recomendada
* Se efetuar a ativação para ativar a melhor prática, o que pode ser o resultado
* Possíveis alternativas para a prática recomendada
* Como pode aprender permitir a melhor prática

Este artigo de melhores práticas de criptografia e segurança dos dados do Azure baseia-se numa opinião de consenso e capacidades da plataforma Azure e conjuntos de recursos, tal como existem no momento que este artigo foi escrito. Opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="choose-a-key-management-solution"></a>Escolher uma solução de gestão de chaves

Proteger as suas chaves é essencial para proteger os seus dados na cloud.

[O Azure Key Vault](../key-vault/key-vault-overview.md) ajuda a salvaguardar chaves criptográficas e segredos na cloud a aplicações e serviços utilizam. A chave de cofre simplifica o processo de gestão de chaves e permite-lhe manter o controlo de teclas que acede e encripta os seus dados. Os desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e, em seguida, migrá-los para chaves de produção. Os administradores de segurança podem conceder (e revogar) a permissão para as chaves, conforme necessário.

Pode utilizar o Key Vault para criar vários contentores seguros, chamados "cofres". Estes cofres são apoiados por HSMs. Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Cofres de chaves também controlam e iniciar o acesso a tudo armazenados nas mesmas. O Azure Key Vault pode processar pedidos e renovações de certificados Transport Layer Security (TLS). Ele fornece recursos para uma solução robusta para gestão de ciclo de vida de certificados.

O Azure Key Vault foi concebido para suportar a aplicação de chaves e segredos. Key Vault não se destina a ser um arquivo para palavras-passe do utilizador.

Seguem-se melhores práticas de segurança para utilizar o Key Vault.

**Melhor prática**: Conceder acesso a utilizadores, grupos e aplicações com um âmbito específico.   
**Detalhe**: Utilize as funções do RBAC de predefinidos. Por exemplo, para conceder acesso a um utilizador para gerir cofres de chaves, seria atribuída a função predefinida [contribuinte do Cofre de chave](../role-based-access-control/built-in-roles.md) a este utilizador com um âmbito específico. O âmbito nesse caso seria uma subscrição, um grupo de recursos ou apenas um cofre de chaves específico. Se as funções predefinidas não atender a suas necessidades, pode [definir suas próprias funções](../role-based-access-control/custom-roles.md).

**Melhor prática**: Controle o que os utilizadores têm acesso aos.   
**Detalhe**: O controlo aos cofres de chaves é controlado através de duas interfaces separadas - o plano de gestão e o plano de dados. Os controlos de acesso do plano de gestão e do plano de dados funcionam de forma independente.

Utilize o RBAC para controlar o que os utilizadores têm acesso a. Por exemplo, se quiser conceder um acesso de aplicação para utilizar as chaves num cofre de chaves, só tem de conceder permissões de acesso do plano de dados ao utilizar políticas de acesso do Cofre de chaves e nenhum acesso do plano de gestão é necessária para esta aplicação. Por outro lado, se deseja que um usuário seja capaz de ler as propriedades do cofre e marcas, mas não tem acesso a chaves, segredos ou certificados, pode conceder esse acesso de leitura do utilizador utilizando o RBAC e sem acesso ao plano de dados é necessário.

**Melhor prática**: Store certificados no seu Cofre de chaves. Os certificados são de alto valor. Em mãos erradas, a segurança dos seus dados ou de segurança de seu aplicativo pode ficar comprometida.   
**Detalhe**: O Azure Resource Manager pode implantar com segurança os certificados armazenados no Azure Key Vault para VMs do Azure quando as VMs são implementadas. Ao definir políticas de acesso apropriados para o Cofre de chaves, também controlar que obtém acesso ao certificado. Outra vantagem é que gerencie todos os seus certificados num local no Azure Key Vault. Ver [implementar certificados para VMs de Cofre de chaves geridas pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) para obter mais informações.

**Melhor prática**: Certifique-se de que não é possível recuperar uma exclusão de cofres de chaves ou objetos de Cofre de chaves.   
**Detalhe**: Eliminação da chave de cofres dos ou objetos de Cofre de chaves podem ser acidental ou maliciosa. Ativar a eliminação de forma recuperável e remover recursos de proteção do Cofre de chaves, especialmente para as chaves que são utilizados para encriptar dados inativos. Eliminação dessas chaves é equivalente a perda de dados, para que possa recuperar cofres eliminados e objetos de cofre, se necessário. Ponha em prática operações de recuperação do Key Vault em intervalos regulares.

> [!NOTE]
> Se um utilizador tiver permissões de contribuinte (RBAC) para um plano de gestão do Cofre de chaves, eles podem conceder próprios acesso para o plano de dados ao definir uma política de acesso do Cofre de chaves. Recomendamos que não é totalmente controlar quem tem acesso de contribuinte aos seus cofres de chaves, para garantir que apenas as pessoas autorizadas podem aceder e gerir cofres de chaves, chaves, segredos e certificados.
>
>

## <a name="manage-with-secure-workstations"></a>Gerir com estações de trabalho seguras

> [!NOTE]
> O administrador da subscrição ou o proprietário deve utilizar uma estação de trabalho de acesso seguro ou uma estação de trabalho de acesso privilegiado.
>
>

Uma vez que a grande maioria dos ataques visar o utilizador final, se torna o ponto final de um dos principais pontos de ataque. Um atacante que compromete o ponto final pode utilizar as credenciais do usuário para obter acesso aos dados da organização. A maioria dos ataques de ponto final tirar partido do fato de que os usuários são administradores em suas estações de trabalho locais.

**Melhor prática**: Utilize uma estação de trabalho de gestão segura para proteger contas sensíveis, tarefas e dados.   
**Detalhe**: Utilize um [estação de trabalho de acesso privilegiado](https://technet.microsoft.com/library/mt634654.aspx) para reduzir a superfície de ataque em estações de trabalho. Estas estações de trabalho de gestão segura podem ajudar a atenuar alguns desses ataques e certifique-se de que os seus dados são mais seguros.

**Melhor prática**: Certifique-se a proteção de ponto final.   
**Detalhe**: Aplicar políticas de segurança em todos os dispositivos que são utilizados para consumir dados, independentemente da localização de dados (na cloud ou no local).

## <a name="protect-data-at-rest"></a>Proteger dados em repouso

[Encriptação de dados em repouso](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório no sentido de soberania de dados, conformidade e privacidade de dados.

**Melhor prática**: Aplica encriptação de disco para o ajudar a salvaguardar os seus dados.   
**Detalhe**: Uso [Azure Disk Encryption](azure-security-disk-encryption.md). Permite que os administradores de TI encriptar discos de VM de IaaS de Linux e Windows. Encriptação de disco combina a funcionalidade do Windows BitLocker de norma da indústria e a funcionalidade de dm-crypt do Linux para fornecer encriptação de volume para o sistema operacional e os discos de dados.

Armazenamento do Azure e a base de dados do Azure SQL encriptam dados inativos, por padrão e muitos encriptação de oferta de serviços, como uma opção. Pode utilizar o Azure Key Vault para manter o controlo das chaves que acedem e encriptam os dados. Ver [suporte do modelo de encriptação de fornecedores do recurso do Azure para saber mais](azure-security-encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Melhores práticas**: Utilize encriptação para ajudar a reduzir os riscos relacionados com o acesso de dados não autorizado.
**Detalhe**: Encripte as suas unidades antes de escrever dados confidenciais aos mesmos.

As organizações que não impõem a encriptação de dados são mais expostas a problemas de confidencialidade de dados. Por exemplo, os utilizadores não autorizados ou não autorizados podem roubar dados em contas comprometidas ou obter acesso não autorizado aos dados codificados em formato não encriptado. As empresas também tem de provar que eles são controles de segurança correto diligentes e utilização para melhorar a segurança de dados para cumprir os regulamentos da indústria.

## <a name="protect-data-in-transit"></a>Proteger dados em trânsito

Proteger dados em trânsito, deve ser uma parte essencial da sua estratégia de proteção de dados. Como dados é ir e vir a partir de vários locais, geralmente recomendamos que utilize sempre os protocolos SSL/TLS para trocar dados entre diferentes localizações. Em algumas circunstâncias, poderá querer isolar o canal de toda comunicação entre o local e na cloud infraestruturas ao utilizar uma VPN.

Para dados mover entre a infraestrutura no local e o Azure, considere as proteções adequadas como HTTPS ou VPN. Ao enviar tráfego encriptado entre uma rede virtual do Azure e uma localização no local através da internet pública, utilize [Gateway de VPN do Azure](https://docs.microsoft.com/azure/vpn-gateway/).

Seguem-se as práticas recomendadas específicas para utilizar HTTPS, SSL/TLS e o Gateway de VPN do Azure.

**Melhor prática**: Acesso seguro a partir de várias estações de trabalho localizados no local a uma rede virtual do Azure.   
**Detalhe**: Uso [VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Melhor prática**: Acesso seguro a partir de uma estação de trabalho individual localizados no local a uma rede virtual do Azure.   
**Detalhe**: Uso [VPN point-to-site](../vpn-gateway/vpn-gateway-point-to-site-create.md).

**Melhor prática**: Mova os conjuntos de dados maiores através de um link WAN de alta velocidade dedicado.   
**Detalhe**: Uso [ExpressRoute](../expressroute/expressroute-introduction.md). Se optar por utilizar o ExpressRoute, também pode criptografar os dados no nível do aplicativo usando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para maior proteção.

**Melhor prática**: Interagir com o armazenamento do Azure através do portal do Azure.   
**Detalhe**: Todas as transações ocorrem através de HTTPS. Também pode utilizar [API do REST de armazenamento](https://msdn.microsoft.com/library/azure/dd179355.aspx) através de HTTPS para interagir com [armazenamento do Azure](https://azure.microsoft.com/services/storage/) e [base de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/).

As organizações que não as protegem os dados em trânsito são mais suscetíveis a [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [escutas](https://technet.microsoft.com/library/gg195641.aspx)e ao seqüestro de sessão. Esses ataques podem ser o primeiro passo para terem acesso aos dados confidenciais.

## <a name="secure-email-documents-and-sensitive-data"></a>Proteger o e-mail, documentos e dados confidenciais

Pretende controlar e proteger o e-mail, documentos e dados confidenciais que partilha fora da sua empresa. [O Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) é uma solução com base na cloud que ajuda uma organização a classificar, Etiquetar e proteger os respetivos documentos e e-mails. Isso pode ser feito automaticamente por administradores que definem regras e condições, manualmente pelos utilizadores ou uma combinação em que os utilizadores obtêm recomendações.

Classificação será sempre identificável, independentemente do onde os dados são armazenados ou com quem são partilhados. As etiquetas incluem marcas visuais como um cabeçalho, rodapé ou marca d'água. São adicionados metadados a ficheiros e cabeçalhos de e-mail em texto não encriptado. O texto não encriptado garante que os outros serviços, tais como soluções para evitar a perda de dados, podem identificar a classificação e tomar as medidas adequadas.

A tecnologia de proteção utiliza o Azure Rights Management (Azure RMS). Esta tecnologia está integrada com outros serviços cloud da Microsoft e aplicações, como o Office 365 e Azure Active Directory. Esta tecnologia de proteção utiliza políticas de encriptação, identidade e autorização. Proteção aplicada através do Azure RMS permanece com os documentos e e-mails, independentemente da localização — dentro ou fora da sua organização, redes, servidores de ficheiros e aplicações.

Esta solução de proteção de informações mantém o utilizador no controlo dos seus dados, mesmo quando são partilhados com outras pessoas. Também pode utilizar o Azure RMS com suas próprias aplicações de linha de negócio e soluções de proteção de informações de fornecedores de software, sejam estas aplicações e soluções no local ou na cloud.

Recomendamos que:

- [Implementar o Azure Information Protection](https://docs.microsoft.com/azure/information-protection/deployment-roadmap) para a sua organização.
- Aplica etiquetas que refletem os seus requisitos empresariais. Por exemplo: Aplica uma etiqueta com o nome "altamente confidencial" para todos os documentos e e-mails que contêm dados top-secret, para classificar e proteger esses dados. Em seguida, apenas os utilizadores autorizados podem aceder estes dados, com as restrições que especificar.
- Configurar [registo de utilização para o Azure RMS](https://docs.microsoft.com/azure/information-protection/log-analyze-usage) para que possa monitorizar a forma como sua organização está a utilizar o serviço de proteção.

As organizações que estão fracas no [classificação de dados](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e proteção de ficheiros pode ser mais suscetível a fuga de dados ou utilização indevida de dados. Com a proteção de arquivos apropriada, pode analisar fluxos de dados para obter informações sobre a sua empresa, detetar comportamentos de risco e tomar medidas corretivas, controlar o acesso a documentos e assim por diante.

## <a name="next-steps"></a>Passos Seguintes

Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais acerca da segurança do Azure e serviços Microsoft relacionados:
* [Blogue de equipa de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as mais recentes da segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – onde podem ser comunicadas as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, ou por e-mail para secure@microsoft.com
