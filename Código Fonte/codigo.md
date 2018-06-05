-- report do historico de batimento 
select mare_maus_usuario, sum(mare_resultado)/count(mare_resultado) "Media Batimento", mare_sessao
    from mack_resultados
    where mare_maus_usuario = :P2_USUARIO
    group by mare_maus_usuario, mare_sessao;

-- validar botão do status
select mast_status from mack_status where mast_seq = 1;

-- mostrar a media dos batimento realizados no momento
declare
medicao number;
valor number;
qtd number;
sessao number;
begin

select mast_ordem into sessao from mack_status where mast_seq = 1;
select sum(mare_resultado), count(mare_resultado) into medicao, qtd from mack_resultados where :P2_USUARIO = mare_maus_usuario and mare_sessao = sessao;
valor := medicao/qtd;
return valor;
exception
    when others then 
        return 0;
end;

-- mostrar resultado da saúde
declare
medicao number;
valor number;
qtd number;
sessao number;
bat varchar2(200);
retorno varchar2(200);
begin

select mast_ordem into sessao from mack_status where mast_seq = 1;
select sum(mare_resultado), count(mare_resultado) into medicao, qtd from mack_resultados where :P2_USUARIO = mare_maus_usuario and mare_sessao = sessao;
valor := medicao/qtd;
if valor is null then
    valor := 0;
end if;
IF valor = 0 THEN
    BAT := 'NÃO FOI POSSÍVEL REALIZAR A MEDIÇÃO!'; 
elsif valor > 100 THEN
    BAT := 'SEU BATIMENTO ESTÁ ACELERADO, VÁ COM CALMA!!!';
elsif valor > 70 AND valor <= 100 THEN
    BAT := 'SEU BATIMENTO ESTÁ NORMAL, CONSIDERAVEL PARA UMA PESSOA SEDENTÁRIA.';
elsif valor <= 50 THEN
    BAT := 'SEU BATIMENTO ESTÁ ÓTIMO, CONSIDERAVEL PARA UMA PESSOA ATLETA!!!';
else
    BAT := 'SEU BATIMENTO ESTÁ NORMAL.';
END IF; 
return BAT;
exception
    when others then 
        RETURN 'NÃO FOI POSSÍVEL REALIZAR A MEDIÇÃO!';
end;
