import { useState } from "react";

const fmt = (n) => {
  const a = Math.abs(n);
  if (a >= 1e9) return `$${(a/1e9).toFixed(1)}B`;
  if (a >= 1e6) return `$${(a/1e6).toLocaleString("es-CO",{maximumFractionDigits:0})}MM`;
  if (a >= 1e3) return `$${(a/1e3).toFixed(0)}K`;
  return `$${a.toLocaleString("es-CO",{maximumFractionDigits:0})}`;
};

function E({ value, onChange, suffix="", prefix="" }) {
  const [ed, setEd] = useState(false);
  const [t, setT] = useState("");
  if (ed) return <input autoFocus value={t} onChange={e=>setT(e.target.value)}
    onBlur={()=>{setEd(false);const v=parseFloat(t.replace(/,/g,""));if(!isNaN(v))onChange(v)}}
    onKeyDown={e=>e.key==="Enter"&&e.target.blur()}
    style={{width:60,padding:"2px 4px",border:"1.5px solid #00B4D8",borderRadius:3,fontSize:13,textAlign:"right",outline:"none",background:"#F0FDFF",color:"#0096B7",fontWeight:600}} />;
  return <span onClick={()=>{setT(String(value));setEd(true)}} style={{cursor:"pointer",color:"#0096B7",fontWeight:600,fontSize:12}}>
    {prefix}{value.toLocaleString("es-CO")}{suffix}
  </span>;
}

export default function App() {
  const [pvp1, setPvp1] = useState(94000);
  const [pvpYoY, setPvpYoY] = useState([0, 5]);
  const [units1, setUnits1] = useState(3000);
  const [unitsYoY, setUnitsYoY] = useState([83, 45]);
  const [iva, setIva] = useState(19);
  const [cogs, setCogs] = useState(27);
  const [mkt, setMkt] = useState(35);
  const [ful, setFul] = useState(10);
  const [ga, setGa] = useState(13);

  const setPYoY = (i,v) => setPvpYoY(prev=>{const n=[...prev];n[i]=v;return n});
  const setUYoY = (i,v) => setUnitsYoY(prev=>{const n=[...prev];n[i]=v;return n});

  const pvp = [pvp1, Math.round(pvp1*(1+pvpYoY[0]/100)), Math.round(pvp1*(1+pvpYoY[0]/100)*(1+pvpYoY[1]/100))];
  const units = [units1, Math.round(units1*(1+unitsYoY[0]/100)), Math.round(units1*(1+unitsYoY[0]/100)*(1+unitsYoY[1]/100))];

  const Y = Array.from({length:3},(_,i)=>{
    const net = pvp[i]/(1+iva/100);
    const r = units[i]*12*net;
    const c = r*cogs/100;
    const gp = r-c;
    const mk = r*mkt/100;
    const fl = r*ful/100;
    const g = r*ga/100;
    return {r,c,gp,mk,fl,g,eb:gp-mk-fl-g};
  });

  const th = {padding:"8px 6px",fontSize:11,fontWeight:700,textAlign:"right",color:"#fff",whiteSpace:"nowrap"};
  const td = {padding:"7px 6px",fontSize:12,textAlign:"right",borderBottom:"1px solid #eee",whiteSpace:"nowrap"};
  const tdL = {...td,textAlign:"left",fontWeight:500,color:"#333",whiteSpace:"normal"};
  const box = {background:"#fff",border:"1px solid #ddd",borderRadius:8,padding:"8px 12px",display:"flex",flexDirection:"column",gap:2,alignItems:"center",textAlign:"center"};
  const yoy = {...td,fontSize:10,color:"#444",fontStyle:"italic",padding:"2px 6px"};
  const yoyL = {...tdL,fontSize:10,color:"#444",fontStyle:"italic",padding:"2px 6px"};
  const nb = {borderBottom:"none"};

  return <div style={{fontFamily:"system-ui,sans-serif",maxWidth:800,margin:"0 auto",padding:16,background:"#fff",color:"#222"}}>

    <h2 style={{fontSize:18,fontWeight:700,margin:"0 0 4px",color:"#1A1A2E"}}>EFFEKT HYDRA — P&L</h2>
    <p style={{fontSize:12,color:"#888",margin:"0 0 16px"}}>Toca los valores en <span style={{color:"#0096B7",fontWeight:600}}>azul</span> para editar</p>

    <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill, minmax(120px, 1fr))",gap:8,marginBottom:20}}>
      <div style={box}><span style={{fontSize:10,color:"#888",fontWeight:600,textTransform:"uppercase",letterSpacing:0.5}}>IVA</span><E value={iva} onChange={setIva} suffix="%"/></div>
      <div style={box}><span style={{fontSize:10,color:"#888",fontWeight:600,textTransform:"uppercase",letterSpacing:0.5}}>COGS</span><E value={cogs} onChange={setCogs} suffix="%"/></div>
      <div style={box}><span style={{fontSize:10,color:"#888",fontWeight:600,textTransform:"uppercase",letterSpacing:0.5}}>Marketing</span><E value={mkt} onChange={setMkt} suffix="%"/></div>
      <div style={box}><span style={{fontSize:10,color:"#888",fontWeight:600,textTransform:"uppercase",letterSpacing:0.5}}>Fulfillment</span><E value={ful} onChange={setFul} suffix="%"/></div>
      <div style={box}><span style={{fontSize:10,color:"#888",fontWeight:600,textTransform:"uppercase",letterSpacing:0.5}}>G&A</span><E value={ga} onChange={setGa} suffix="%"/></div>
    </div>

    <div style={{overflowX:"auto",WebkitOverflowScrolling:"touch"}}>
    <table style={{width:"100%",borderCollapse:"collapse",tableLayout:"fixed"}}>
      <colgroup><col style={{width:"35%"}}/><col style={{width:"21.6%"}}/><col style={{width:"21.6%"}}/><col style={{width:"21.6%"}}/></colgroup>
      <thead><tr style={{background:"#1A1A2E"}}><th style={{...th,textAlign:"left"}}>Concepto</th>{[1,2,3].map(i=><th key={i} style={th}>Año {i}</th>)}</tr></thead>
      <tbody>
        <tr><td style={{...tdL,...nb}}>PVP (COP)</td><td style={{...td,...nb}}><E value={pvp1} onChange={setPvp1} prefix="$"/></td>{[1,2].map(i=><td key={i} style={{...td,...nb,color:"#333"}}>${pvp[i].toLocaleString("es-CO")}</td>)}</tr>
        <tr><td style={yoyL}>YoY</td><td style={yoy}></td>{pvpYoY.map((v,i)=><td key={i} style={yoy}><E value={v} onChange={x=>setPYoY(i,x)} suffix="%"/></td>)}</tr>
        <tr><td style={{...tdL,...nb}}>Unidades / mes</td><td style={{...td,...nb}}><E value={units1} onChange={v=>setUnits1(Math.round(v))}/></td>{[1,2].map(i=><td key={i} style={{...td,...nb,color:"#333"}}>{units[i].toLocaleString("es-CO")}</td>)}</tr>
        <tr><td style={yoyL}>YoY</td><td style={yoy}></td>{unitsYoY.map((v,i)=><td key={i} style={yoy}><E value={v} onChange={x=>setUYoY(i,x)} suffix="%"/></td>)}</tr>
        <tr style={{background:"#F3F4F6"}}><td style={{...tdL,fontWeight:700,...nb}}>Ventas netas</td>{Y.map((y,i)=><td key={i} style={{...td,fontWeight:700,...nb}}>{fmt(y.r)}</td>)}</tr>
        <tr style={{background:"#F3F4F6"}}><td style={yoyL}>YoY</td>{Y.map((y,i)=><td key={i} style={yoy}>{i===0?"":`${((y.r/Y[i-1].r-1)*100).toFixed(1)}%`}</td>)}</tr>
        <tr><td style={tdL}>(-) COGS</td>{Y.map((y,i)=><td key={i} style={td}>{fmt(y.c)}</td>)}</tr>
        <tr style={{background:"#E8F8FD"}}><td style={{...tdL,fontWeight:700,...nb}}>Utilidad Bruta</td>{Y.map((y,i)=><td key={i} style={{...td,fontWeight:700,...nb}}>{fmt(y.gp)}</td>)}</tr>
        <tr style={{background:"#E8F8FD"}}><td style={yoyL}>Margen bruto</td>{Y.map((y,i)=><td key={i} style={yoy}>{(y.gp/y.r*100).toFixed(1)}%</td>)}</tr>
        <tr><td style={tdL}>(-) Marketing</td>{Y.map((y,i)=><td key={i} style={td}>{fmt(y.mk)}</td>)}</tr>
        <tr><td style={tdL}>(-) Fulfillment</td>{Y.map((y,i)=><td key={i} style={td}>{fmt(y.fl)}</td>)}</tr>
        <tr><td style={tdL}>(-) G&A</td>{Y.map((y,i)=><td key={i} style={td}>{fmt(y.g)}</td>)}</tr>
        <tr style={{background:"#E8F8FD",borderTop:"2px solid #00B4D8"}}><td style={{...tdL,fontWeight:700,fontSize:13,...nb}}>= EBITDA</td>{Y.map((y,i)=><td key={i} style={{...td,fontWeight:700,fontSize:13,...nb}}>{fmt(y.eb)}</td>)}</tr>
        <tr style={{background:"#E8F8FD"}}><td style={yoyL}>Margen EBITDA</td>{Y.map((y,i)=>{const m=y.eb/y.r*100;return <td key={i} style={yoy}>{m.toFixed(1)}%</td>})}</tr>
      </tbody>
    </table>
    </div>

    <p style={{fontSize:10,color:"#aaa",marginTop:12,textAlign:"center"}}>EFFEKT Hydra · Cifras en COP · Estimaciones propias</p>
  </div>;
}
