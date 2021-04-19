---
title: Como configurar funções Azure com uma rede virtual
description: Artigo que mostra como executar certas tarefas de networking virtual para Funções Azure.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: c123b20e163731f9a872a969f2f1564479b6e308
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718436"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Como configurar funções Azure com uma rede virtual

Este artigo mostra-lhe como executar tarefas relacionadas com a configuração da sua aplicação de função para ligar e executar numa rede virtual. Para saber mais sobre as Funções Azure e a rede, consulte [as opções de networking do Azure Functions](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restringir a sua conta de armazenamento a uma rede virtual 

Quando criar uma aplicação de função, deve criar ou ligar para uma conta de Armazenamento Azure de uso geral que suporte o armazenamento de Blob, Queue e Table. Pode substituir esta conta de armazenamento por uma que esteja segura com pontos finais de serviço ou ponto final privado. 

> [!NOTE]  
> Atualmente, esta funcionalidade funciona para todos os SKUs virtuais suportados pela rede do Windows no plano Dedicado (Serviço de Aplicações) e para planos Premium. O plano de consumo não é apoiado. 

Para criar uma função com uma conta de armazenamento restrita a uma rede privada:

1. Crie uma função com uma conta de armazenamento que não tenha pontos finais de serviço ativados.

1. Configure a função para ligar à sua rede virtual.

1. Criar ou configurar uma conta de armazenamento diferente.  Esta será a conta de armazenamento que asseguramos com os pontos finais de serviço e conectamos a nossa função.

1. [Crie uma partilha de ficheiros](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) na conta de armazenamento segura.

1. Ativar os pontos finais do serviço ou o ponto final privado para a conta de armazenamento.  
    * Se utilizar ligações privadas de ponto final, a conta de armazenamento necessitará de um ponto final privado para os `file` `blob` sub-recursos e sub-recursos.  Se utilizar certas capacidades como Funções Duradouras, também necessitará e será acessível através de `queue` uma `table` ligação de ponto final privado.
    * Se utilizar pontos finais de serviço, ative a sub-rede dedicada às suas aplicações de função para contas de armazenamento.

1. Copie o conteúdo do ficheiro e do blob da conta de armazenamento de aplicações de função para a conta de armazenamento e partilha de ficheiros seguras.

1. Copie o fio de ligação para esta conta de armazenamento.

1. Atualizar as Definições de **Aplicação** em **Configuração** para a aplicação de função para o seguinte:

    | Nome da definição | Valor | Comentário |
    |----|----|----|
    | `AzureWebJobsStorage`| Cadeia de conexão de armazenamento | Esta é a cadeia de ligação para uma conta de armazenamento segura. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Cadeia de conexão de armazenamento | Esta é a cadeia de ligação para uma conta de armazenamento segura. |
    | `WEBSITE_CONTENTSHARE` | Partilha de ficheiros | O nome da partilha de ficheiros criada na conta de armazenamento segura onde residem os ficheiros de implantação do projeto. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nova definição |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Força todo o tráfego de saída através da rede virtual. Requerido quando a conta de armazenamento estiver a utilizar ligações de ponto final privado. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | O servidor DNS utilizado pela aplicação. Requerido quando a conta de armazenamento estiver a utilizar ligações de ponto final privado. |

1. **Selecione Guardar** para guardar as definições de aplicação. Alterar as definições da aplicação faz com que a aplicação reinicie.  

Após o reinício da aplicação de função, está agora ligado a uma conta de armazenamento segura.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Opções de rede das Funções do Azure](functions-networking-options.md)

