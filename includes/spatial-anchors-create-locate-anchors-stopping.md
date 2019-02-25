## <a name="pausing-resetting-or-stopping-the-session"></a>Colocar em pausa, repor ou a parar a sessão

Para parar a sessão temporariamente, pode invocar o método Stop. Se o fizer, irá parar todos os observadores e processamento de ambiente, mesmo que invoca ProcessFrame(). Em seguida, pode invocar Start() para retomar o processamento. Quando a retomar, dados de ambiente já capturados na sessão são mantidos.
