---
title: Guia unidade de teste da máquina virtual no Portal do Cloud Partner para o Azure Marketplace
description: Descreve a guia Test Drive usada na criação de uma oferta de VM do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 94ebbcca50916f1675ab990a4b45f3b90e069104
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808863"
---
# <a name="virtual-machine-test-drive-tab"></a>Guia unidade de teste da máquina virtual

A guia **Test Drive** da nova página de **oferta** permite que você forneça a seus clientes potenciais uma demonstração prática e Autoguiada dos principais recursos e benefícios do seu produto, demonstrados em um cenário padronizado.  O Test Drive é um recurso opcional para os tipos de oferta que dão suporte ao Test Drive.  O Test Drive requer que os ativos de suporte sejam implementados corretamente.  Para obter mais informações, consulte o artigo [test drive do Azure Marketplace](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Para habilitar esse recurso, na guia **Test Drive** , clique na opção **Sim** em **habilitar um test drive**.  A guia **Test Drive** exibe os campos disponíveis para edição.  Um asterisco anexado (*) no nome do campo indica que ele é necessário.

![Guia Test Drive no novo formulário de oferta para máquinas virtuais](./media/publishvm_007.png)


## <a name="field-values"></a>Valores de campo

A tabela a seguir descreve a finalidade e o conteúdo desses campos.  Os campos obrigatórios são indicted por um asterisco (*).


|    Campo                  |       Descrição                                                            |
|  ---------                |     ---------------                                                          |
|  *Detalhes*   |  |
| **Descrição\***           | Forneça uma visão geral do cenário do Test Drive. Esse texto será mostrado ao usuário enquanto o Test Drive estiver sendo provisionado. Este campo oferece suporte a HTML básico se você quiser fornecer conteúdo formatado.  |
| **\* manual do usuário**           | Carregue um manual de usuário Detalhado (. pdf) que ajuda a testar os usuários do drive a entender como usar sua solução.  |
| **Vídeo de demonstração do Test Drive** | Carregue um vídeo que demonstra sua solução.  Se você escolher essa opção, deverá fornecer um nome, uma URL para o vídeo (hospedado no YouTube ou Vimeo) e uma miniatura (533x324 pixel) para o vídeo. |
| *Configuração técnica* |  |
| **Instâncias\***             | Especifique a disponibilidade de região e relativamente disponibilidade da instância de VM (clique no ícone de informações para obter mais detalhes).  <br/>As sessões de unidade de teste simultâneas potenciais não devem exceder o limite de cota para sua assinatura.  O primeiro é calculado como: [número de regiões selecionadas] x [instâncias quentes] + [número de regiões selecionadas] x [instâncias quentes] + [número de regiões selecionadas] x [instâncias frias] |
| **Duração da unidade de teste\***   | Duração máxima da sessão em horas. A sessão do Test Drive é encerrada automaticamente após esse período de tempo ser excedido.  |
|**Testar o modelo ARM\***| Carregue o modelo de Azure Resource Manager associado a este Test Drive. Para obter mais informações, consulte [transformando o modelo de implantação de máquina virtual para o Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Informações de acesso\***    | Azure Resource Manager informações de logon e de avaliação de acesso, escritas como texto sem formatação ou HTML simples. |
| *Detalhes da assinatura de implantação do Test Drive* |  |
| **ID da assinatura do Azure\*** | Pode ser obtido entrando no [portal do Microsoft Azure](https://ms.portal.azure.com) e clicando em **assinaturas** na barra de menus à esquerda. (Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Esse identificador deve ser um GUID do formulário `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **ID de locatário do Azure AD\***    | Azure Active Directory ID do locatário.  Pode ser obtido entrando na [portal do Microsoft Azure](https://ms.portal.azure.com) e clicando em **Azure Active Directory** na barra de menus à esquerda e, em seguida, clicando em **Propriedades** na barra de menus do meio, copiando a **ID do diretório** do formulário.  Esse identificador também deve ser um GUID.  Se estiver em branco, você deverá criar uma ID de locatário para sua organização. |
| **ID de Aplicativo Azure AD\***       | Identificador para sua solução de VM do Azure registrada  |
| **Chave de Aplicativo Azure AD\***      | Chave de autenticação para sua solução registrada |
|   |   |


## <a name="next-steps"></a>Passos seguintes

Na próxima guia do [Marketplace](./cpp-marketplace-tab.md) , você fornecerá informações legais e de marketing sobre sua solução.
