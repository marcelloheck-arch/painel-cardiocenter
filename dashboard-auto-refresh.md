# Auto-refresh do dashboard (17/jul)

**Problema:** o dashboard busca os dados só uma vez (ao carregar a página) ou quando você clica manualmente num botão de período. Não existe nenhum timer — por isso precisa de F5 ou clique para atualizar.

**Onde:** `dashboard.html`, logo no fim do arquivo, dentro da tag `<script>`, próximo à linha 815 (`window.addEventListener('load', ...)`).

**O que fazer:** colar isto **logo depois** do bloco `window.addEventListener('load', ...)` que já existe (não apagar nada, só acrescentar):

```javascript
// Auto-refresh: re-busca os dados do período atual a cada 60 segundos,
// sem recarregar a página. Não mexe nos filtros escolhidos pelo usuário.
setInterval(() => {
  if (_ultimaBusca) {
    buscar(_ultimaBusca[0], _ultimaBusca[1]);
  }
}, 60000);
```

## Por que funciona

A variável `_ultimaBusca` já existe no código (usada em `confirmarToken()`, linha 502) e guarda o período que está sendo exibido no momento (hoje, semana, mês, ou intervalo personalizado). O `setInterval` só repete a mesma chamada `buscar()` com esse período, a cada 60 segundos — como se você clicasse no mesmo botão de novo, só que sozinho.

Não interfere em nada: se você trocar de "Hoje" para "Semana", o `_ultimaBusca` atualiza, e o próximo ciclo do timer já busca o período novo.

## Ajustar o intervalo (opcional)

`60000` = 60 segundos. Se quiser mais ou menos frequente, troque o número (em milissegundos): `30000` para 30s, `120000` para 2 minutos. Como é só leitura de dados agregados (não é chat em tempo real), 60s é um bom equilíbrio entre atualização e não sobrecarregar o `WEBHOOK DASHBOARD` com requisições.

## Teste

1. Abra o dashboard, selecione um período.
2. Espere 60 segundos sem tocar em nada.
3. Confirme que os números atualizam sozinhos (gere um card de teste ou conclua um durante a espera para ver a mudança).
4. Confirme que trocar de período continua funcionando normalmente.

Sem risco: o dashboard é só leitura, então um refresh automático não pode causar nenhuma ação indesejada (diferente do painel de atendimento, que tem botões de ação).
