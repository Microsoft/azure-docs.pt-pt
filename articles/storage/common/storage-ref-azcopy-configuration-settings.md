---
title: Definição de configuração AzCopy v10 (Azure Storage) | Microsoft Docs
description: Este artigo fornece informações de referência para as definições de configuração AzCopy V10.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509120"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>Definições de configuração AzCopy v10 (Azure Storage)

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo contém uma lista de variáveis ambientais que pode usar para configurar a AzCopy v10.

> [!NOTE]
> Se procura conteúdo para ajudá-lo a começar com a AzCopy, consulte [Get start with AzCopy](storage-use-azcopy-v10.md).

## <a name="azcopy-v10-environment-variables"></a>Variáveis ambientais AzCopy v10

A tabela seguinte descreve cada variável de ambiente e fornece links para conteúdos que podem ajudá-lo a usar a variável.

| Variável de ambiente | Description |
|--|--|
| AWS_ACCESS_KEY_ID | Chave de acesso aos Serviços Web da Amazon. Fornece uma chave para autorizar com a Amazon Web Services. [Copiar dados do Amazon S3 para o Azure Storage utilizando o AzCopy](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | A chave de acesso secreto da Amazon Web Services fornece uma chave secreta para autorizar com os Serviços Web da Amazon. [Copiar dados do Amazon S3 para o Azure Storage utilizando o AzCopy](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | O ponto final do Diretório Ativo Azure para utilizar. Esta variável é utilizada apenas para o login automático, utilize a bandeira da linha de comando ao invocar o comando de login. |
| AZCOPY_AUTO_LOGIN_TYPE | Desa esta variável para `DEVICE` `MSI` , ou . `SPN` . Esta variável proporciona a capacidade de autorizar sem usar o `azcopy login` comando. Este mecanismo é útil nos casos em que o seu sistema operativo não tenha uma loja secreta, como um *porta-chaves Linux.* Ver [Autorizar sem uma loja secreta.](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_BUFFER_GB | Especifique a quantidade máxima da sua memória do sistema que pretende que o AzCopy utilize ao descarregar e carregar ficheiros. Expresse este valor em gigabytes (GB). Ver [Otimizar o uso da memória](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Por padrão, o AzCopy no Windows irá cache de procuração de servidores ao nível do nome de hospedeiro (não tendo em conta o caminho do URL). Desative o cache em qualquer outro valor que não o "verdadeiro". |
| AZCOPY_CONCURRENCY_VALUE | Especifica o número de pedidos simultâneos que podem ocorrer. Pode usar esta variável para aumentar a produção. Se o seu computador tiver menos de 5 CPUs, então o valor desta variável está definido para `32` . Caso contrário, o valor predefinido é igual a 16 vezes o número de CPUs. O valor predefinido máximo desta variável `3000` é, mas pode definir manualmente este valor mais ou menos. Ver [Aumentar a concordância](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Sobrepõe-se ao número (aproximado) de ficheiros que estão em curso a qualquer momento, controlando quantos ficheiros simultaneamente iniciamos transferências. |
| AZCOPY_CONCURRENT_SCAN | Controla o (máximo) grau de paralelismo utilizado durante a digitalização. Apenas afeta enumeradores paralelos, que incluem ficheiros/blobs Azure e sistemas de ficheiros locais. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Substitui a versão API do serviço para que o AzCopy possa acomodar ambientes personalizados como o Azure Stack. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Só se aplica quando a Azure Blobs é a fonte. A digitalização simultânea é mais rápida, mas emprega a API de listagem hierárquica, o que pode resultar em mais IOs/custo. Especifique 'verdadeiro' para sacrificar o desempenho, mas economize no custo. |
| AZCOPY_JOB_PLAN_LOCATION | Substitui-se onde são armazenados os ficheiros do plano de trabalho (utilizados para rastreio e retoma de progresso), para evitar o enchimento de um disco. |
| AZCOPY_LOG_LOCATION | Substitui os ficheiros de registo onde os ficheiros de registo são armazenados, para evitar o enchimento de um disco. |
| AZCOPY_MSI_CLIENT_ID | A identificação do cliente de uma identidade gerida atribuída pelo utilizador. Utilize quando `AZCOPY_AUTO_LOGIN_TYPE` estiver programado para `MSI` . Ver [Autorizar sem uma loja secreta](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | O ID do objeto da identidade gerida atribuída pelo utilizador. Utilize quando `AZCOPY_AUTO_LOGIN_TYPE` estiver programado para `MSI` . Ver [Autorizar sem uma loja secreta](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | O ID de recursos da identidade gerida atribuída pelo utilizador. Ver [Autorizar sem uma loja secreta](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | A produção de bolhas de página deve ser automaticamente ajustada para corresponder aos limites de Serviço? A predefinição é verdadeiro. Definido para 'falso' para desativar |
| AZCOPY_PARALLEL_STAT_FILES | Faz com que a AzCopy procure propriedades de ficheiros em 'threads' paralelos ao digitalizar o sistema de ficheiros local.  Os fios são retirados da piscina definidos por AZCOPY_CONCURRENT_SCAN.  Definir isto para verdade pode melhorar o desempenho de digitalização no Linux.  Não é necessário nem recomendado no Windows. |
| AZCOPY_SHOW_PERF_STATES | Se definido, para qualquer coisa, a saída no ecrã incluirá contagens de pedaços por estado |
| AZCOPY_SPA_APPLICATION_ID | O ID da aplicação do registo da aplicação do seu serviço. Utilize quando `AZCOPY_AUTO_LOGIN_TYPE` estiver programado para `SPN` . Ver [Autorizar sem uma loja secreta](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | A senha de um certificado. Utilize quando `AZCOPY_AUTO_LOGIN_TYPE` estiver programado para `SPN` . Ver [Autorizar sem uma loja secreta](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | O caminho relativo ou totalmente qualificado para um ficheiro de certificado. Utilize quando `AZCOPY_AUTO_LOGIN_TYPE` estiver programado para `SPN` . Ver [Autorizar sem uma loja secreta](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | O segredo do cliente. Utilize quando `AZCOPY_AUTO_LOGIN_TYPE` estiver programado para `SPN` . Ver [Autorizar sem uma loja secreta](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | O ID do inquilino do Azure Ative Directory para usar para o login interativo do dispositivo OAuth. Esta variável é utilizada apenas para o login automático, utilize a bandeira da linha de comando ao invocar o comando de login. |
| AZCOPY_TUNE_TO_CPU | Definido como falso para evitar que a AzCopy tome em consideração a utilização do CPU ao afinar automaticamente o seu nível de concurrency (por exemplo, no comando de referência). |
| AZCOPY_USER_AGENT_PREFIX | Adicione um prefixo ao agente utilizador AzCopy predefinido, que é utilizado para fins de telemetria. Um espaço é inserido automaticamente. |
| GOOGLE_APPLICATION_CREDENTIALS | O caminho absoluto para o ficheiro chave da conta de serviço fornece uma chave para autorizar com o Google Cloud Storage. [Copie os dados do Google Cloud Storage para o Azure Storage utilizando o AzCopy (pré-visualização)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Configura as definições de procuração para AzCopy. Desaprote esta variável para o endereço IP proxy e número de porta proxy. Por exemplo, `xx.xxx.xx.xxx:xx`. Se executar o AzCopy no Windows, o AzCopy detetará automaticamente as definições de proxy, para que não tenha de utilizar esta definição no Windows. Se optar por utilizar esta definição no Windows, sobrepor-se-á à deteção automática. Consulte [configurações de procuração configurar](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>Configurar definições de proxy

Para configurar as definições de procuração para AzCopy, defina a `HTTPS_PROXY` variável ambiental. Se executar o AzCopy no Windows, o AzCopy detetará automaticamente as definições de proxy, para que não tenha de utilizar esta definição no Windows. Se optar por utilizar esta definição no Windows, sobrepor-se-á à deteção automática.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | Numa utilização rápida de comando: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> Na utilização do PowerShell: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Atualmente, a AzCopy não suporta proxies que requerem autenticação com NTLM ou Kerberos.

### <a name="bypassing-a-proxy"></a>Contornar um representante

Se estiver a executar a AzCopy no Windows e quiser dizer-lhe para não utilizar _nenhum_ representante (em vez de detetar automaticamente as definições) utilize estes comandos. Com estas definições, a AzCopy não procurará nem tentará utilizar qualquer procuração.

| Sistema operativo | Ambiente | Comandos  |
|--------|-----------|----------|
| **Windows** | Pedido de comando (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Noutros sistemas operativos, basta deixar a HTTPS_PROXY variável desaparada se não pretender utilizar nenhum representante.

## <a name="see-also"></a>Ver também

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Otimizar o desempenho do AzCopy v10 com Azure Storage](storage-use-azcopy-optimize.md)
- [Problemas problemas problemas AzCopy V10 em armazenamento Azure usando ficheiros de log](storage-use-azcopy-configure.md)
