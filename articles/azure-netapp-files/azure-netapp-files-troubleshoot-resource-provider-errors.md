---
title: Resolver problemas de erros do fornecedor de recursos de ficheiros do Azure NetApp | Documentos da Microsoft
description: Descreve as causas, soluções e soluções alternativas para erros comuns do fornecedor de recursos de ficheiros do Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769440"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Resolver problemas de erros do Fornecedor de Recursos do Azure NetApp Files
Este artigo descreve o fornecedor de recursos de ficheiros do Azure NetApp erros comuns, suas causas, soluções e soluções alternativas. 

<a name="error_01"></a>***O Azure Key Vault não configurado.***   
O Azure Key Vault armazena as credenciais necessárias para aceder à API subjacente. Este erro indica que o Azure Key Vault não recebeu as credenciais de completas para aceder à API subjacente.

* Causa  
O Azure Key Vault não recebeu as credenciais corretas ou as credenciais estão incompletas.  

* Solução   
O serviço de ficheiros de NetApp do Azure utiliza o Azure Key Vault. O Azure Key Vault autentica através de um token do Azure Active Directory. Por conseguinte, o proprietário da aplicação tem de registar a aplicação no Azure Active Directory.

* Solução   
Nenhum.  O Azure Key Vault tem de ser definido corretamente para usar arquivos de NetApp do Azure.  

<a name="error_02"></a>***Criação do Token não pode ser alterado.***   
Este erro ocorre quando tentar alterar o token de criação, depois do volume tiver sido criado.
Token de criação tem de ser definido quando o volume é criado e não é possível alterar mais tarde.

* Causa   
Está a tentar alterar o token de criação, depois do volume tiver sido criado, que não é uma operação suportada.

* Solução   
Depois do Volume tiver sido criado, considere remover o parâmetro no pedido para descartar a mensagem de erro.

* Solução   
Se precisar de alterar o token de criação, pode criar um novo volume com um novo token de criação e, em seguida, migrar os dados para o novo volume.


<a name="error_03"></a>***Token de criação tem de ser, pelo menos, 16 carateres de comprimento.***   
Este erro ocorre quando o token de criação não cumpre o requisito de comprimento. O comprimento do token de criação tem de ter, pelo menos, 16 carateres.

* Causa   
O token de criação não cumpre o requisito de comprimento.  Quando cria um volume com a API, um token de criação é necessário. Se estiver a utilizar o portal, o token pode ser gerado automaticamente.

* Solução   
Aumenta a duração do token de criação. Por exemplo, pode adicionar outra palavra no início ou fim do token de criação.

* Solução   
O mínimo necessário não é possível ignorar o comprimento do token de criação.  Pode usar um prefixo ou sufixo para aumentar a duração do token de criação.


<a name="error_04"></a>***Erro ao eliminar o volume que não foi encontrado na NetApp serviço ficheiros do Azure.***   
Este erro ocorreu porque o registo interna de recursos está a sincronizar.

* Causa   
O volume poderão permanecer apresentado no portal do durante algum tempo depois do mesmo foi eliminado. Se eliminar o volume com a API, é possível que o volume não foi especificado corretamente. O erro também pode ser causado pela cache do browser desatualizado.

* Solução   
Cache do browser clara se estiver a utilizar o portal. Também é uma cache interna que é atualizado a cada 10 minutos.  Pode tentar limpar o cache novamente.  Se o problema persistir após 10 minutos, pode criar um pedido de suporte.

* Solução   
Utilize um volume diferente nesse meio tempo e ignorar a já existente.


<a name="error_05"></a>***Erro ao inserir um novo Volume em ficheiros de NetApp do Azure.***   
Este erro ocorre porque o registo interna de recursos está a sincronizar.

* Causa   
O volume poderá continuam apresentado no portal do durante algum tempo depois do mesmo foi eliminado. Se eliminar o volume com a API, é possível que o volume não foi especificado corretamente.

* Solução   
Se estiver a utilizar o portal, o volume já foi criado.  O volume deve aparecer automaticamente. Se o problema persistir, pode criar um pedido de suporte.

* Solução   
Pode criar um volume com um nome diferente e um token de criação de diferentes.


<a name="error_06"></a>***O nome de caminho de ficheiro pode conter letras, números e hífenes (""-"") apenas.***   
Este erro ocorre quando o caminho do ficheiro contém carateres não suportados, por exemplo, um período ("."), vírgula (","), um caráter de sublinhado ("\_"), ou o símbolo de dólar norte-americano ("$").

* Causa   
O caminho do ficheiro contém carateres não suportados, por exemplo, um período ("."), vírgula (","), um caráter de sublinhado ("\_"), ou o símbolo de dólar norte-americano ("$").

* Solução   
Remover os carateres que não são alfabéticos letras, números ou hífenes ("-") do caminho de ficheiro que introduziu.

* Solução   
Pode substituir um caráter de sublinhado com um hífen ou utilize capitalização em vez de espaços para indicar o início de novas palavras (por exemplo, usando "NewVolume" em vez de "novo volume").


<a name="error_07"></a>***Não é possível alterar o ID do volume.***   
Este erro ocorre quando tentar alterar o ID do volume.  Alterar o ID do volume não é uma operação suportada.

* Causa   
O ID de sistema de ficheiros é definido quando o volume é criado. O ID do volume não pode ser alterado posteriormente.

* Solução   
Nenhum.

* Solução   
Nenhum.  O ID do volume é gerado quando o volume é criado e não pode ser alterado posteriormente.


<a name="error_08"></a>***Um valor inválido "{0}" foi recebido para {1}.***   
Esta mensagem indica um erro nos campos para RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 e Nfsv4.

* Causa   
O pedido de validação de entrada falhou para, pelo menos, um dos seguintes campos: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 e Nfsv4.

* Solução   
Certifique-se definir todos os parâmetros obrigatórios e não conflitantes na linha de comandos. Por exemplo, não é possível definir os UnixReadOnly UnixReadWrite parâmetros e ao mesmo tempo.

* Solução   
Consulte a seção de solução.  


<a name="error_09"></a>***Valor em falta para '{0}'.***   
Este erro indica que um atributo obrigatório está em falta no pedido de, pelo menos, um dos seguintes parâmetros: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 e Nfsv4.

* Causa   
O pedido de validação de entrada falhou para, pelo menos, um dos seguintes campos: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 e Nfsv4.

* Solução   
Certifique-se definir todos os parâmetros obrigatórios e não conflitantes na linha de comandos. Por exemplo, não é possível definir os UnixReadOnly UnixReadWrite parâmetros e ao mesmo tempo

* Solução   
Consulte a seção de solução.  


<a name="error_10"></a> ***{0} já está em utilização.***   
Este erro indica que o nome para o recurso já foi utilizado.

* Causa   
Está a tentar criar um volume com um nome que é o mesmo que um volume existente.

* Solução   
Utilize um nome exclusivo ao criar um volume.

* Solução   
Se necessário, pode alterar o nome do volume existente para que o novo volume, pode utilizar o nome pretendido.


<a name="error_11"></a> ***{0} demasiado curto.***   
Este erro indica que o nome do volume não cumpre o requisito de comprimento mínimo.

* Causa   
O nome do volume é demasiado curto.

* Solução   
Aumente o comprimento do nome do volume.  

* Solução   
Pode adicionar um prefixo ou sufixo comuns para o nome do volume.


<a name="error_12"></a>***API de ficheiros de NetApp Azure inacessível.***   
A API do Azure conta com a API de ficheiros NetApp do Azure para gerir volumes.  Este erro indica um problema com a ligação de API.

* Causa   
A API subjacente não está a responder, resultando num erro interno. Este erro é provável que seja temporária.

* Solução   
O problema é provável que seja temporária.  O pedido deve ter êxito após algum tempo.

* Solução   
Nenhum. A API básica é essencial para a gestão de volumes.  


<a name="error_13"></a>***Não existem credenciais encontrado para a subscrição "{0}'.***   
Este erro indica que as credenciais fornecidas são inválidos ou não tem sido definidas corretamente na subscrição.

* Causa   
As credenciais que são inválidos ou incorretamente definida impedem o acesso ao serviço para a gestão de volumes.

* Solução   
Certifique-se de que as credenciais são estabelecidas e introduziu corretamente na linha de comandos.

* Solução   
Nenhum.  Definir credenciais corretamente é essencial para utilizar o serviço ficheiros do Azure NetApp.  


<a name="error_14"></a>***Foi encontrado nenhum id de resultado da operação para '{0}'.***   
Este erro indica que um erro interno está a impedir a conclusão da operação.

* Causa   
Erro interno Ocorreu e impediu a conclusão da operação.

* Solução   
Este erro é provável que seja temporária.  Aguarde alguns minutos e tente novamente. Se o problema persistir, crie um pedido de suporte técnico investigar o problema.

* Solução   
Aguarde alguns minutos e verifique se o problema persistir.


<a name="error_15"></a>***Operação "{0}' não suportado.***   
Este erro indica que o comando não está disponível para subscrição Active Directory ou ao recurso.

* Causa   
A operação não está disponível para subscrição ou ao recurso.

* Solução   
Certifique-se de que o comando é introduzido corretamente e disponível para os recursos e subscrição que está a utilizar.

* Solução   
Consulte a seção de solução.  


<a name="error_16"></a>***A operação de correção não é suportada para este tipo de recurso.***   
Este erro ocorre quando tentar alterar o destino de montagem ou um instantâneo.

* Causa   
O destino de montagem é definido quando é criado e não pode ser alterado posteriormente.

* Solução   
Nenhum.  O destino de montagem não pode ser alterado depois de criar o volume.

* Solução   
Nenhum.


<a name="error_17"></a>***Foi recebido um valor para a propriedade só de leitura '{0}'.***   
Este erro ocorre quando define um valor para uma propriedade que não pode ser alterado. Por exemplo, não é possível alterar o ID do volume.

* Causa   
Tentou modificar um parâmetro (por exemplo, o ID de volume) que não pode ser alterado.

* Solução   
Nenhum. Não é possível modificar o parâmetro para o ID de volume.

* Solução   
O ID do volume não deve requerer a modificação.  Por conseguinte, não é necessária uma solução alternativa.

<a name="error_18"></a>***O pedido {0} não foi encontrado.***   
Este erro ocorre quando tenta fazer referência a um recurso não existente, por exemplo, um volume ou um instantâneo. O recurso pode ter sido eliminado ou tem um nome de recurso misspelt.

* Causa   
Está tentando fazer referência um recurso não existente (por exemplo, um volume ou um instantâneo) que já tenha sido eliminado ou que tenha um nome de recurso incorretamente digitada.

* Solução   
Verifique o pedido para erros de ortografia para se certificar de que está corretamente referenciada.

* Solução   
Consulte a seção de solução.

<a name="error_19"></a>***Não é possível obter as credenciais para a subscrição "{0}'.***   
Este erro indica que as credenciais fornecidas são inválidos ou incorretamente definida na subscrição.

* Causa   
As credenciais que são inválidas ou incorretamente o conjunto na subscrição impedir o acesso ao serviço para a gestão de volumes.

* Solução   
Certifique-se de que as credenciais são estabelecidas e introduziu corretamente na linha de comandos.

* Solução   
Nenhum.  Corretamente as credenciais do conjunto são essenciais para a utilização de ficheiros do Azure NetApp.

<a name="error_20"></a>***Erro de ficheiros NetApp Azure desconhecido.***   
A API do Azure conta com a API de ficheiros NetApp do Azure para gerir volumes. O erro indica um problema na comunicação para a API.

* Causa   
A API básica está enviando um erro desconhecido.  Este erro é provável que seja temporária.

* Solução   
O problema é provável que seja temporária, e o pedido deve ter êxito após algum tempo. Se o problema persistir, crie um pedido de suporte para que o problema investigado.

* Solução   
Nenhum.  A API básica é essencial para a gestão de volumes.

<a name="error_21"></a>***Valor recebido para uma propriedade desconhecida '{0}'.***   
Este erro ocorre quando propriedades inexistentes são fornecidas para um recurso, como o volume, o instantâneo ou o destino de montagem.

* Causa   
O pedido tem um conjunto de propriedades que podem ser utilizadas com cada recurso.  Não pode incluir quaisquer propriedades não existentes no pedido.

* Solução   
Certifique-se de que todos os nomes de propriedade estão escritos corretamente e as propriedades estão disponíveis para os recursos e subscrição.

* Solução   
Reduza o número de propriedades definidas no pedido para eliminar a propriedade que está causando o erro.


<a name="error_22"></a>***Operação de atualização não é suportada para este tipo de recurso.***   
Apenas os volumes podem ser atualizados. Este erro ocorre quando tentar efetuar uma operação de atualização não suportado, por exemplo, a atualização de um instantâneo.

* Causa   
O recurso que está a tentar atualizar não suporta a operação de atualização.  Apenas os volumes podem ter suas propriedades modificadas.

* Solução   
Nenhum.  O recurso que está a tentar atualizar não suporta a operação de atualização. Por conseguinte, não pode ser alterado.

* Solução   
Para um volume, crie um novo recurso com a atualização no local e migrar os dados.


<a name="error_23"></a>***Número de itens: {0} para o objeto: {1} é externo mínima-máxima intervalo.***   
Este erro ocorre quando as regras de política de exportação não cumpre o requisito de intervalo mínimo ou máximo.  Se definir a política de exportação, tem de ter uma exportação regra de política no mínimo e cinco regras de política de exportação no máximo.

* Causa   
A política de exportação que definiu não cumpre o intervalo necessário.  

* Solução   
Certifique-se de que o índice não esteja já utilizado e que se encontra no intervalo de 1 a 5.

* Solução   
Não é obrigatório para usar a diretiva de exportação nos volumes. Por conseguinte, pode omitir a política de exportação se não precisa de ter regras de política de exportação.


<a name="error_24"></a>***Erro de valor para o objeto de duplicação {0}.***   
Este erro ocorre quando a política de exportação não está definida com um índice exclusivo.  Quando é possível definir políticas de exportação, todas as regras de política de exportação tem de ter um índice exclusivo entre 1 e 5.

* Causa   
A política de exportação definidos não cumpre o requisito para regras de política de exportação. Tem de ter uma exportação regra de política no mínimo e cinco regras de política de exportação no máximo.  

* Solução   
Certifique-se de que o índice não esteja já utilizado e que está a ser o intervalo de 1 a 5.

* Solução   
Utilize um índice diferente para a regra que está a tentar definir.


