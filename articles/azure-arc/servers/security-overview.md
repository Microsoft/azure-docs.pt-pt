---
title: Descrição geral da segurança
description: Informações de segurança sobre os servidores ativados do Azure Arc.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: aa8653b783e7eb3e211b7514831604dd5642cfbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117032"
---
# <a name="azure-arc-for-servers-security-overview"></a>Azure Arc para visão geral de segurança dos servidores

Este artigo descreve a configuração de segurança e considerações que deve avaliar antes de implementar servidores ativados pelo Azure Arc na sua empresa.

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

Cada servidor Azure Arc habilitado tem uma identidade gerida como parte de um grupo de recursos dentro de uma subscrição do Azure, esta identidade representa o servidor que executa no local ou outro ambiente de nuvem. O acesso a este recurso é controlado pelo controlo de acesso baseado em funções padrão [Azure.](../../role-based-access-control/overview.md) A partir da página [**Controlo de Acesso (IAM)**](../../role-based-access-control/role-assignments-portal.md) no portal Azure, pode verificar quem tem acesso ao seu servidor ativado Azure Arc.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Azure Arc permitiu o controlo de acesso ao servidor" border="false" lightbox="./media/security-overview/access-control-page.png":::

Os utilizadores e aplicações [que](../../role-based-access-control/built-in-roles.md#contributor) concedem ao contribuinte ou administrador o acesso ao recurso podem escoar alterações ao recurso, incluindo a implantação ou eliminação de [extensões](manage-vm-extensions.md) na máquina. As extensões podem incluir scripts arbitrários que funcionam num contexto privilegiado, por isso considere qualquer colaborador no recurso Azure como um administrador indireto do servidor.

A **função de Azure Connected Machine Onboarding** está disponível para o embarque à escala e só é capaz de ler ou criar novos servidores ativados pelo Arco em Azure. Não pode ser utilizado para eliminar servidores já registados ou gerir extensões. Como uma boa prática, recomendamos apenas atribuir este papel ao diretor de serviço Azure Ative (Azure AD) usado para máquinas a bordo em escala.

Os utilizadores como membro da função **de Administrador de Recursos da Máquina Azure Podem** ler, modificar, reonboard e eliminar uma máquina. Esta função destina-se a suportar a gestão de servidores ativados pela Arc, mas não outros recursos no grupo de recursos ou subscrição.

## <a name="agent-security-and-permissions"></a>Segurança do agente e permissões

Para gerir o agente Azure Connected Machine (azcmagent) no Windows, a sua conta de utilizador precisa de ser membro do grupo de Administradores locais. No Linux, deve ter permissões de acesso à raiz.

O agente Azure Connected Machine é composto por três serviços, que funcionam na sua máquina.

* O serviço Hybrid Instance Medata Service (himds) é responsável por todas as funcionalidades centrais do Arc. Isto inclui o envio de batimentos cardíacos para o Azure, expondo um serviço de metadados de instância local para outras aplicações para aprender sobre o ID de recursos Azure da máquina, e recuperar fichas AD AZure para autenticar para outros serviços Azure. Este serviço funciona como uma conta de serviço virtual desprivileçada no Windows, e como o utilizador **de eleds** no Linux.

* O serviço de Configuração de Hóspedes (GCService) é responsável pela avaliação da Política de Azure na máquina.

* O serviço de extensão de configuração do hóspede (ExtensionService) é responsável pela instalação, atualização e eliminação de extensões (agentes, scripts ou outro software) na máquina.

Os serviços de configuração e extensão de hóspedes funcionam como Sistema Local no Windows, e como raiz no Linux.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Usando uma identidade gerida com servidores ativados arc

Por predefinição, o sistema Azure Ative Directory atribuído à identidade utilizada pela Arc só pode ser utilizado para atualizar o estado do servidor ativado pelo Arco em Azure. Por exemplo, o último estado do batimento cardíaco *visto.* Pode atribuir opcionalmente funções adicionais à identidade se uma aplicação no seu servidor utilizar a identidade atribuída ao sistema para aceder a outros serviços Azure.

Embora o Serviço de Metadados de Instância Híbrida possa ser acedido por qualquer aplicação em execução na máquina, apenas as aplicações autorizadas podem solicitar um token AD Azure para a identidade atribuída ao sistema. Na primeira tentativa de aceder ao URI simbólico, o serviço gerará uma bolha criptográfica gerada aleatoriamente num local no sistema de ficheiros que apenas os chamadores fidedignos podem ler. O autor da chamada deve então ler o ficheiro (provando que tem a permissão adequada) e voltar a tentar o pedido com o conteúdo do ficheiro no cabeçalho de autorização para recuperar com sucesso um token AD Azure.

* No Windows, o chamador deve ser um membro do grupo de **administradores** locais ou do grupo **de aplicações de extensão do agente híbrido** para ler a bolha.

* Em Linux, o chamador deve ser um membro do grupo **de himds** para ler a bolha.

## <a name="using-disk-encryption"></a>Usando encriptação de disco

O agente Azure Connected Machine utiliza a autenticação de chave pública para comunicar com o serviço Azure. Depois de embarcar num servidor para Azure Arc, uma chave privada é guardada no disco e usada sempre que o agente comunica com o Azure. Se roubada, a chave privada pode ser usada noutro servidor para comunicar com o serviço e agir como se fosse o servidor original. Isto inclui o acesso ao sistema de identidade atribuída e quaisquer recursos a que a identidade tenha acesso. O ficheiro chave privado está protegido apenas para permitir que a conta **de itds** o leia. Para evitar ataques offline, recomendamos vivamente a utilização de encriptação completa do disco (por exemplo, BitLocker, dm-crypt, etc.) no volume do sistema operativo do seu servidor.

## <a name="next-steps"></a>Passos seguintes

Antes de avaliar ou ativar os servidores ativados pelo Arc em várias máquinas híbridas, [reveja a visão geral do agente da Máquina Conectada](agent-overview.md) para compreender os requisitos, detalhes técnicos sobre o agente e métodos de implementação.
