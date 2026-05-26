import { useState, useEffect, useRef } from "react";
import { LineChart, Line, XAxis, YAxis, Tooltip, ResponsiveContainer, Area, AreaChart } from "recharts";
 
// ─── FONTS & GLOBAL STYLES ────────────────────────────────────────────────────
const FONTS = `@import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;1,9..40,300&display=swap');`;
 
const CSS = `
*{margin:0;padding:0;box-sizing:border-box}
:root{
  --bg:#080808;--surface:#111;--card:#161616;--border:#1e1e1e;--border2:#2a2a2a;
  --white:#f0ece6;--muted:#666;--dim:#333;
  --red:#e8342a;--orange:#f07030;--green:#7ec87e;--blue:#4a9eff;--yellow:#f5c842;
}
html{scroll-behavior:smooth}
body{background:var(--bg);color:var(--white);font-family:'DM Sans',sans-serif;min-height:100vh;-webkit-font-smoothing:antialiased}
button{cursor:pointer;font-family:'DM Sans',sans-serif}
input,select{font-family:'DM Sans',sans-serif}
::-webkit-scrollbar{width:4px}
::-webkit-scrollbar-track{background:var(--bg)}
::-webkit-scrollbar-thumb{background:var(--dim);border-radius:2px}
 
/* ── LANDING ── */
.landing{min-height:100vh;display:flex;flex-direction:column}
.nav{display:flex;align-items:center;justify-content:space-between;padding:24px 40px;border-bottom:1px solid var(--border)}
.nav-logo{font-family:'Bebas Neue',sans-serif;font-size:22px;letter-spacing:.08em;color:var(--white)}
.nav-logo span{color:var(--red)}
.nav-cta{background:var(--red);color:var(--white);border:none;padding:10px 22px;font-size:13px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;border-radius:3px;transition:background .2s}
.nav-cta:hover{background:#c4261d}
 
.hero{flex:1;display:flex;flex-direction:column;justify-content:center;padding:80px 40px 60px;max-width:900px;margin:0 auto;width:100%}
.hero-eyebrow{font-size:11px;letter-spacing:.2em;text-transform:uppercase;color:var(--red);font-weight:500;margin-bottom:20px}
.hero-title{font-family:'Bebas Neue',sans-serif;font-size:clamp(64px,12vw,120px);line-height:.88;letter-spacing:.02em;margin-bottom:32px}
.hero-title em{color:var(--red);font-style:normal}
.hero-sub{font-size:17px;color:var(--muted);font-weight:300;line-height:1.7;max-width:480px;margin-bottom:48px}
.hero-actions{display:flex;gap:16px;flex-wrap:wrap;align-items:center}
.btn-primary{background:var(--red);color:var(--white);border:none;font-family:'Bebas Neue',sans-serif;font-size:20px;letter-spacing:.08em;padding:18px 40px;border-radius:3px;transition:all .2s}
.btn-primary:hover{background:#c4261d;transform:translateY(-1px)}
.btn-ghost{background:transparent;color:var(--muted);border:1px solid var(--border2);font-size:13px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;padding:14px 24px;border-radius:3px;transition:all .2s}
.btn-ghost:hover{border-color:var(--white);color:var(--white)}
 
.features{display:grid;grid-template-columns:repeat(3,1fr);gap:1px;background:var(--border);border-top:1px solid var(--border)}
@media(max-width:600px){.features{grid-template-columns:1fr}}
.feature{background:var(--bg);padding:32px;display:flex;flex-direction:column;gap:10px}
.feature-icon{font-size:22px;margin-bottom:4px}
.feature-title{font-size:13px;font-weight:500;letter-spacing:.04em;text-transform:uppercase;color:var(--white)}
.feature-desc{font-size:13px;color:var(--muted);line-height:1.6;font-weight:300}
 
/* ── APP SHELL ── */
.shell{max-width:820px;margin:0 auto;padding:40px 24px 100px}
.back-btn{display:inline-flex;align-items:center;gap:8px;font-size:12px;letter-spacing:.1em;text-transform:uppercase;color:var(--muted);background:none;border:none;padding:0 0 32px;transition:color .2s}
.back-btn:hover{color:var(--white)}
 
/* ── TABS ── */
.tabs{display:flex;gap:0;border-bottom:1px solid var(--border);margin-bottom:40px}
.tab{background:none;border:none;border-bottom:2px solid transparent;color:var(--muted);font-size:13px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;padding:14px 20px;transition:all .2s;margin-bottom:-1px}
.tab:hover{color:var(--white)}
.tab.active{color:var(--white);border-bottom-color:var(--red)}
 
/* ── SECTION HEADER ── */
.sec-eyebrow{font-size:11px;letter-spacing:.18em;text-transform:uppercase;color:var(--red);font-weight:500;margin-bottom:10px}
.sec-title{font-family:'Bebas Neue',sans-serif;font-size:clamp(36px,6vw,56px);line-height:.92;margin-bottom:16px}
.sec-sub{font-size:14px;color:var(--muted);font-weight:300;line-height:1.65;max-width:460px;margin-bottom:36px}
 
/* ── FORM ── */
.form-grid{display:grid;grid-template-columns:1fr 1fr;gap:18px;margin-bottom:18px}
@media(max-width:520px){.form-grid{grid-template-columns:1fr}}
.field{display:flex;flex-direction:column;gap:9px}
.field.full{grid-column:1/-1}
label{font-size:11px;letter-spacing:.12em;text-transform:uppercase;color:var(--muted);font-weight:500}
.select-wrap{position:relative}
.select-wrap::after{content:'↓';position:absolute;right:14px;top:50%;transform:translateY(-50%);color:var(--muted);pointer-events:none;font-size:13px}
select,input[type=number],input[type=text]{background:var(--card);border:1px solid var(--border2);color:var(--white);font-size:15px;padding:13px 16px;border-radius:4px;outline:none;appearance:none;transition:border-color .2s;width:100%}
select:focus,input:focus{border-color:var(--red)}
.toggle-group{display:flex;gap:8px;flex-wrap:wrap}
.tog{padding:10px 16px;border:1px solid var(--border2);background:var(--card);color:var(--muted);font-size:13px;font-weight:500;border-radius:3px;transition:all .15s;letter-spacing:.03em}
.tog:hover{border-color:var(--dim);color:var(--white)}
.tog.on{background:var(--red);border-color:var(--red);color:var(--white)}
 
.gen-btn{margin-top:32px;width:100%;background:var(--red);color:var(--white);border:none;font-family:'Bebas Neue',sans-serif;font-size:22px;letter-spacing:.06em;padding:20px;border-radius:4px;transition:background .2s,transform .1s}
.gen-btn:hover{background:#c4261d}
.gen-btn:active{transform:scale(.99)}
.gen-btn:disabled{background:var(--dim);color:var(--muted);cursor:not-allowed}
 
/* ── LOADING ── */
.loading-wrap{margin-top:48px}
.loading-label{font-size:11px;letter-spacing:.14em;text-transform:uppercase;color:var(--muted);margin-bottom:12px}
.lbar{height:2px;background:var(--border);border-radius:2px;overflow:hidden}
.lbar-inner{height:100%;background:linear-gradient(90deg,var(--red),var(--orange));animation:lload 1.6s ease-in-out infinite}
@keyframes lload{0%{width:0%;margin-left:0%}50%{width:70%;margin-left:15%}100%{width:0%;margin-left:100%}}
 
/* ── PLAN ── */
.plan{animation:fadeUp .45s ease both}
@keyframes fadeUp{from{opacity:0;transform:translateY(18px)}to{opacity:1;transform:translateY(0)}}
 
.plan-header{border-top:1px solid var(--border);padding-top:28px;margin-bottom:32px;display:flex;justify-content:space-between;align-items:flex-start;gap:16px;flex-wrap:wrap}
.plan-title{font-family:'Bebas Neue',sans-serif;font-size:32px;letter-spacing:.04em}
.pills{display:flex;gap:10px;flex-wrap:wrap}
.pill{font-size:11px;letter-spacing:.1em;text-transform:uppercase;color:var(--muted);border:1px solid var(--border2);padding:5px 12px;border-radius:2px}
.pill strong{color:var(--orange);font-weight:500}
 
/* volume chart */
.chart-wrap{background:var(--card);border:1px solid var(--border);border-radius:6px;padding:20px 20px 8px;margin-bottom:28px}
.chart-title{font-size:11px;letter-spacing:.12em;text-transform:uppercase;color:var(--muted);margin-bottom:16px}
 
/* week cards */
.week-card{border:1px solid var(--border);border-radius:6px;margin-bottom:12px;overflow:hidden;transition:border-color .2s}
.week-card:hover{border-color:var(--border2)}
.week-hdr{display:flex;justify-content:space-between;align-items:center;padding:14px 18px;background:var(--card);cursor:pointer;user-select:none;gap:12px}
.week-left{display:flex;align-items:center;gap:14px}
.wlabel{font-family:'Bebas Neue',sans-serif;font-size:17px;letter-spacing:.06em}
.wtag{font-size:10px;text-transform:uppercase;letter-spacing:.1em;font-weight:500;padding:3px 8px;border-radius:2px}
.wtag.build{color:var(--orange);background:rgba(240,112,48,.1)}
.wtag.recovery{color:var(--blue);background:rgba(74,158,255,.08)}
.wtag.peak{color:var(--red);background:rgba(232,52,42,.1)}
.wtag.taper{color:var(--green);background:rgba(126,200,126,.08)}
.week-right{display:flex;align-items:center;gap:14px}
.wkm{font-size:13px;color:var(--muted);font-style:italic}
.wcheck{font-size:13px;color:var(--green)}
.chevron{color:var(--muted);font-size:13px;transition:transform .2s}
.chevron.open{transform:rotate(180deg)}
 
.week-body{padding:18px}
.day-row{display:grid;grid-template-columns:70px 1fr 56px 36px;gap:10px;align-items:center;padding:9px 0;border-bottom:1px solid var(--border);transition:background .15s}
.day-row:last-child{border-bottom:none}
.day-row:hover{background:rgba(255,255,255,.015)}
.dname{font-size:11px;text-transform:uppercase;letter-spacing:.1em;color:var(--muted);font-weight:500}
.dwkt{font-size:13.5px;color:var(--white);line-height:1.4}
.drest{color:var(--dim);font-style:italic}
.dkm{font-size:12px;color:var(--orange);font-weight:500;text-align:right}
.done-btn{background:none;border:1px solid var(--border);color:var(--muted);width:28px;height:28px;border-radius:50%;font-size:11px;transition:all .2s;display:flex;align-items:center;justify-content:center}
.done-btn:hover{border-color:var(--green);color:var(--green)}
.done-btn.checked{background:var(--green);border-color:var(--green);color:#fff}
 
.progress-bar-wrap{margin-bottom:8px}
.pbar-label{display:flex;justify-content:space-between;font-size:11px;color:var(--muted);letter-spacing:.06em;text-transform:uppercase;margin-bottom:6px}
.pbar{height:3px;background:var(--border);border-radius:2px;overflow:hidden}
.pbar-fill{height:100%;background:linear-gradient(90deg,var(--red),var(--orange));border-radius:2px;transition:width .4s ease}
 
/* ── PACING CALC ── */
.calc-grid{display:grid;grid-template-columns:1fr 1fr;gap:18px}
@media(max-width:520px){.calc-grid{grid-template-columns:1fr}}
.result-card{background:var(--card);border:1px solid var(--border);border-radius:6px;padding:24px;margin-top:24px}
.result-title{font-size:11px;letter-spacing:.14em;text-transform:uppercase;color:var(--muted);margin-bottom:20px}
.result-row{display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid var(--border)}
.result-row:last-child{border-bottom:none}
.result-label{font-size:13px;color:var(--muted)}
.result-val{font-family:'Bebas Neue',sans-serif;font-size:22px;letter-spacing:.04em;color:var(--orange)}
.result-val.big{font-size:32px;color:var(--red)}
 
/* ── RACE PREDICTOR ── */
.predict-result{background:var(--card);border:1px solid var(--border);border-radius:6px;padding:28px;margin-top:24px;display:grid;grid-template-columns:repeat(2,1fr);gap:1px;background:var(--border)}
.pred-cell{background:var(--card);padding:20px}
.pred-race{font-size:11px;letter-spacing:.12em;text-transform:uppercase;color:var(--muted);margin-bottom:8px}
.pred-time{font-family:'Bebas Neue',sans-serif;font-size:28px;letter-spacing:.04em;color:var(--white)}
.pred-pace{font-size:12px;color:var(--muted);margin-top:4px}
 
/* ── TRACKER ── */
.tracker-empty{text-align:center;padding:60px 20px;color:var(--muted);font-size:14px;font-style:italic}
.log-form{display:grid;grid-template-columns:1fr 1fr 1fr auto;gap:12px;align-items:end;margin-bottom:28px}
@media(max-width:600px){.log-form{grid-template-columns:1fr 1fr}}
.log-btn{background:var(--red);color:#fff;border:none;padding:13px 20px;border-radius:4px;font-size:13px;font-weight:500;letter-spacing:.04em;white-space:nowrap;transition:background .2s}
.log-btn:hover{background:#c4261d}
.log-table{width:100%;border-collapse:collapse}
.log-table th{font-size:10px;letter-spacing:.12em;text-transform:uppercase;color:var(--muted);text-align:left;padding:8px 12px;border-bottom:1px solid var(--border)}
.log-table td{font-size:13px;padding:12px 12px;border-bottom:1px solid var(--border);color:var(--white)}
.log-table tr:last-child td{border-bottom:none}
.log-table td:last-child{color:var(--muted)}
.del-btn{background:none;border:none;color:var(--dim);font-size:16px;transition:color .2s;padding:0 4px}
.del-btn:hover{color:var(--red)}
.stats-row{display:grid;grid-template-columns:repeat(3,1fr);gap:12px;margin-bottom:28px}
@media(max-width:520px){.stats-row{grid-template-columns:1fr 1fr}}
.stat-card{background:var(--card);border:1px solid var(--border);border-radius:6px;padding:16px 18px}
.stat-label{font-size:10px;letter-spacing:.12em;text-transform:uppercase;color:var(--muted);margin-bottom:6px}
.stat-val{font-family:'Bebas Neue',sans-serif;font-size:26px;letter-spacing:.04em;color:var(--white)}
.stat-unit{font-size:11px;color:var(--muted);margin-left:4px;font-family:'DM Sans',sans-serif}
 
/* ── ACTIONS ── */
.divider{width:36px;height:2px;background:var(--red);margin:44px 0 32px}
.action-row{display:flex;gap:12px;flex-wrap:wrap}
.act-btn{flex:1;min-width:160px;border:1px solid var(--border2);background:transparent;color:var(--muted);font-size:12px;letter-spacing:.08em;text-transform:uppercase;padding:14px 18px;border-radius:3px;transition:all .2s}
.act-btn:hover{border-color:var(--white);color:var(--white)}
 
/* ── TOOLTIP ── */
.custom-tooltip{background:var(--surface);border:1px solid var(--border2);border-radius:4px;padding:10px 14px;font-size:12px;color:var(--white)}
.custom-tooltip .label{color:var(--muted);font-size:10px;letter-spacing:.1em;text-transform:uppercase;margin-bottom:4px}
.custom-tooltip .val{font-family:'Bebas Neue',sans-serif;font-size:20px;color:var(--orange)}
 
@media print{
  .nav,.tabs,.back-btn,.action-row,.gen-btn,.done-btn,.log-form,.del-btn{display:none!important}
  .shell{padding:20px}
  .week-body{display:block!important}
  body{background:#fff;color:#000}
  .week-card,.result-card,.chart-wrap{border:1px solid #ddd}
}
`;
 
// ─── CONSTANTS ────────────────────────────────────────────────────────────────
const DAYS = ["Mon","Tue","Wed","Thu","Fri","Sat","Sun"];
const WEEKS_FOR_RACE = {"5k":8,"10k":10,"half":12,"marathon":16};
const RACE_KM = {"5k":5,"10k":10,"half":21.0975,"marathon":42.195};
const RACE_LABELS = {"5k":"5K","10k":"10K","half":"Half Marathon","marathon":"Full Marathon"};
 
// ─── PLAN GENERATION ──────────────────────────────────────────────────────────
const BASE_KM = {beginner:25,intermediate:45,advanced:70};
const PEAK_KM = {beginner:50,intermediate:75,advanced:110};
 
const RUN_SLOTS = {3:[0,2,5],4:[0,2,4,6],5:[0,1,3,5,6],6:[0,1,2,4,5,6],7:[0,1,2,3,4,5,6]};
const WORKOUT_TYPES = {
  3:["easy","tempo","long"],
  4:["easy","intervals","easy","long"],
  5:["easy","intervals","easy","tempo","long"],
  6:["easy","intervals","easy","tempo","easy","long"],
  7:["easy","intervals","easy","tempo","easy","medium","long"],
};
const KM_MULT = {easy:.8,tempo:1.0,intervals:.9,long:1.5,medium:1.2};
 
function describeWorkout(type, km, isBeginner){
  return {
    easy:`Easy run — conversational pace, keep heart rate low. Aerobic base builder.`,
    tempo:`Tempo — warm up 2km, run ${Math.max(3,Math.round(km*.6))}km at comfortably hard pace, cool down 2km.`,
    intervals:`Intervals — ${isBeginner?"4×400m at 5K effort":"6×800m at threshold pace"}, 90s jog recovery between reps.`,
    long:`Long run — slow & steady. ${km}km at easy pace. Your most important session of the week.`,
    medium:`Medium-long — ${km}km at easy/moderate effort. Builds endurance between long runs.`,
  }[type] || "Easy run";
}
 
function generatePlan({race,level,days,weeks}){
  const runDays = parseInt(days);
  const isBeginner = level==="beginner";
  const plan = [];
  for(let w=1;w<=weeks;w++){
    const progress = w/weeks;
    const isTaper = w >= weeks-(race==="marathon"?2:1);
    const isPeak = !isTaper && progress>0.7;
    const isRecovery = w%4===0 && !isTaper;
    let type = "build";
    if(isTaper) type="taper";
    else if(isPeak) type="peak";
    else if(isRecovery) type="recovery";
    const taperF = isTaper ? Math.max(.4, .65-(w-(weeks-2))*.12) : 1;
    const recF = isRecovery ? .8 : 1;
    const targetKm = Math.round((BASE_KM[level]+(PEAK_KM[level]-BASE_KM[level])*Math.min(progress*1.4,1))*taperF*recF);
    const sessionKm = Math.round(targetKm/runDays);
    const slots = RUN_SLOTS[runDays]||RUN_SLOTS[4];
    const wtypes = type==="taper"
      ? ["easy","tempo","easy","easy","easy","easy","easy"].slice(0,runDays)
      : type==="recovery"
      ? Array(runDays).fill("easy")
      : (WORKOUT_TYPES[runDays]||WORKOUT_TYPES[4]);
    const dayPlans = DAYS.map(d=>({day:d,rest:true,workout:"Rest & Recovery",km:0,type:"rest"}));
    slots.forEach((slot,i)=>{
      const wt = wtypes[i]||"easy";
      const km = Math.max(4,Math.round(sessionKm*(KM_MULT[wt]||1)));
      dayPlans[slot]={day:DAYS[slot],rest:false,workout:describeWorkout(wt,km,isBeginner),km,type:wt};
    });
    plan.push({week:w,type,totalKm:targetKm,days:dayPlans});
  }
  return plan;
}
 
// ─── PACING UTILS ─────────────────────────────────────────────────────────────
function secToHMS(s){
  const h=Math.floor(s/3600),m=Math.floor((s%3600)/60),sec=Math.round(s%60);
  return h>0?`${h}:${String(m).padStart(2,"0")}:${String(sec).padStart(2,"0")}`:`${m}:${String(sec).padStart(2,"0")}`;
}
function hmsToSec(h,m,s){return (+h)*3600+(+m)*60+(+s||0)}
function paceStr(secPerKm){return secToHMS(secPerKm)+"/km"}
 
// Riegel formula
function predictTime(refTime, refDist, targetDist){
  return refTime * Math.pow(targetDist/refDist, 1.06);
}
 
// ─── COMPONENTS ───────────────────────────────────────────────────────────────
function CustomTooltip({active,payload,label}){
  if(!active||!payload?.length) return null;
  return(
    <div className="custom-tooltip">
      <div className="label">Week {label}</div>
      <div className="val">{payload[0].value} km</div>
    </div>
  );
}
 
function VolumeChart({plan}){
  const data = plan.map(w=>({week:w.week,km:w.totalKm,type:w.type}));
  const colors = {build:"#f07030",recovery:"#4a9eff",peak:"#e8342a",taper:"#7ec87e"};
  return(
    <div className="chart-wrap">
      <div className="chart-title">Weekly Volume (km)</div>
      <ResponsiveContainer width="100%" height={140}>
        <AreaChart data={data} margin={{top:4,right:4,left:-20,bottom:0}}>
          <defs>
            <linearGradient id="volGrad" x1="0" y1="0" x2="0" y2="1">
              <stop offset="0%" stopColor="#e8342a" stopOpacity={.3}/>
              <stop offset="100%" stopColor="#e8342a" stopOpacity={0}/>
            </linearGradient>
          </defs>
          <XAxis dataKey="week" tick={{fontSize:10,fill:"#666"}} axisLine={false} tickLine={false}/>
          <YAxis tick={{fontSize:10,fill:"#666"}} axisLine={false} tickLine={false}/>
          <Tooltip content={<CustomTooltip/>}/>
          <Area type="monotone" dataKey="km" stroke="#e8342a" strokeWidth={2} fill="url(#volGrad)" dot={(props)=>{
            const c=colors[data[props.index]?.type]||"#e8342a";
            return <circle key={props.index} cx={props.cx} cy={props.cy} r={3} fill={c} stroke={c}/>;
          }}/>
        </AreaChart>
      </ResponsiveContainer>
    </div>
  );
}
 
// ─── TABS ─────────────────────────────────────────────────────────────────────
const TABS = ["Plan","Pacing","Predictor","Tracker"];
 
// ─── MAIN APP ─────────────────────────────────────────────────────────────────
export default function App(){
  const [screen, setScreen] = useState("landing"); // landing | app
  const [tab, setTab] = useState("Plan");
 
  // Form
  const [race, setRace] = useState("marathon");
  const [level, setLevel] = useState("intermediate");
  const [days, setDays] = useState("4");
  const [goalH, setGoalH] = useState("4");
  const [goalM, setGoalM] = useState("00");
 
  // Plan
  const [plan, setPlan] = useState(null);
  const [loading, setLoading] = useState(false);
  const [expanded, setExpanded] = useState({});
  const [done, setDone] = useState({}); // {weekIdx_dayIdx: bool}
 
  // Pacing calc
  const [pcH, setPcH] = useState("4");
  const [pcM, setPcM] = useState("00");
  const [pcS, setPcS] = useState("00");
  const [pcRace, setPcRace] = useState("marathon");
 
  // Race predictor
  const [rpH, setRpH] = useState("0");
  const [rpM, setRpM] = useState("50");
  const [rpS, setRpS] = useState("00");
  const [rpDist, setRpDist] = useState("10");
 
  // Tracker
  const [logDate, setLogDate] = useState("");
  const [logKm, setLogKm] = useState("");
  const [logNote, setLogNote] = useState("");
  const [logs, setLogs] = useState([]);
 
  function handleGenerate(){
    setLoading(true); setPlan(null);
    setTimeout(()=>{
      const p = generatePlan({race,level,days,weeks:WEEKS_FOR_RACE[race]});
      setPlan(p); setLoading(false); setExpanded({0:true});
    },1100);
  }
 
  function toggleWeek(i){setExpanded(p=>({...p,[i]:!p[i]}))}
 
  function toggleDone(wi,di){
    const k=`${wi}_${di}`;
    setDone(p=>({...p,[k]:!p[k]}));
  }
 
  function weekProgress(wi){
    if(!plan) return 0;
    const w = plan[wi];
    const runDays = w.days.filter(d=>!d.rest).length;
    if(!runDays) return 0;
    const doneCount = w.days.filter((d,di)=>!d.rest && done[`${wi}_${di}`]).length;
    return Math.round((doneCount/runDays)*100);
  }
 
  // Pacing
  function calcPacing(){
    const totalSec = hmsToSec(pcH,pcM,pcS);
    if(!totalSec) return null;
    const dist = RACE_KM[pcRace];
    const paceSecKm = totalSec/dist;
    const splits = [dist*.25,dist*.5,dist*.75,dist].map(d=>({
      label:d===dist?"Finish":`${Math.round(d)}km`,
      time:secToHMS(paceSecKm*d),
      pace:paceStr(paceSecKm),
    }));
    return {pace:paceStr(paceSecKm), splits, paceSecKm,
      zones:{
        easy:paceStr(paceSecKm*1.25),
        tempo:paceStr(paceSecKm*1.07),
        threshold:paceStr(paceSecKm*1.02),
        race:paceStr(paceSecKm),
        vo2:paceStr(paceSecKm*.95),
      }
    };
  }
 
  // Predictor
  function calcPredictor(){
    const refSec = hmsToSec(rpH,rpM,rpS);
    const refDist = parseFloat(rpDist)||10;
    if(!refSec||!refDist) return null;
    return Object.entries(RACE_KM).map(([r,d])=>({
      race:r,
      label:RACE_LABELS[r],
      time:secToHMS(predictTime(refSec,refDist,d)),
      pace:paceStr(predictTime(refSec,refDist,d)/d),
    }));
  }
 
  function addLog(){
    if(!logKm||!logDate) return;
    setLogs(p=>[{id:Date.now(),date:logDate,km:parseFloat(logKm),note:logNote},...p]);
    setLogKm(""); setLogNote("");
  }
 
  const totalLogKm = logs.reduce((a,l)=>a+l.km,0);
  const avgLogKm = logs.length? (totalLogKm/logs.length).toFixed(1) : 0;
 
  const pcResult = calcPacing();
  const prResult = calcPredictor();
 
  // ── LANDING PAGE ──────────────────────────────────────────────────────────
  if(screen==="landing") return (
    <>
      <style>{FONTS+CSS}</style>
      <div className="landing">
        <nav className="nav">
          <div className="nav-logo">PACE<span>FORGE</span></div>
          <button className="nav-cta" onClick={()=>setScreen("app")}>Start Free →</button>
        </nav>
        <div className="hero">
          <div className="hero-eyebrow">Science-backed training</div>
          <h1 className="hero-title">
            YOUR RACE.<br/>
            YOUR <em>PLAN.</em><br/>
            YOUR PR.
          </h1>
          <p className="hero-sub">
            Enter your goal and fitness level. Get a fully personalized training plan with pace targets, weekly volume, and progress tracking — built in seconds.
          </p>
          <div className="hero-actions">
            <button className="btn-primary" onClick={()=>setScreen("app")}>BUILD MY PLAN →</button>
            <button className="btn-ghost" onClick={()=>{setScreen("app");setTab("Pacing")}}>Pacing Calculator</button>
          </div>
        </div>
        <div className="features">
          {[
            {icon:"🗓️",title:"Full Training Plans",desc:"8–16 week plans for 5K, 10K, Half & Full Marathon. Proper periodization with build, peak, recovery and taper phases."},
            {icon:"⏱️",title:"Pacing Calculator",desc:"Enter your goal time. Get your race pace, split targets, and training pace zones for every type of workout."},
            {icon:"🔮",title:"Race Predictor",desc:"Know one race time? We'll predict your potential across all distances using the proven Riegel formula."},
            {icon:"📈",title:"Progress Tracker",desc:"Log every run, track your weekly volume, and watch your training build over time with visual charts."},
            {icon:"✅",title:"Workout Check-ins",desc:"Tick off each session as you complete it. See your week-by-week completion rate at a glance."},
            {icon:"🖨️",title:"Print & Export",desc:"Save your plan as a PDF or print it for the fridge. Your training follows you everywhere."},
          ].map(f=>(
            <div className="feature" key={f.title}>
              <div className="feature-icon">{f.icon}</div>
              <div className="feature-title">{f.title}</div>
              <div className="feature-desc">{f.desc}</div>
            </div>
          ))}
        </div>
      </div>
    </>
  );
 
  // ── APP SHELL ─────────────────────────────────────────────────────────────
  return (
    <>
      <style>{FONTS+CSS}</style>
      <div className="shell">
        <button className="back-btn" onClick={()=>setScreen("landing")}>← PaceForge</button>
 
        {/* TABS */}
        <div className="tabs">
          {TABS.map(t=>(
            <button key={t} className={`tab ${tab===t?"active":""}`} onClick={()=>setTab(t)}>{t}</button>
          ))}
        </div>
 
        {/* ── TAB: PLAN ── */}
        {tab==="Plan"&&(
          <>
            <div className="sec-eyebrow">Training Plan Generator</div>
            <div className="sec-title">BUILD YOUR<br/>PLAN.</div>
            <p className="sec-sub">Tell us your goal, fitness level, and available days. We'll handle the science.</p>
 
            <div className="form-grid">
              <div className="field">
                <label>Target Race</label>
                <div className="select-wrap">
                  <select value={race} onChange={e=>setRace(e.target.value)}>
                    <option value="5k">5K</option>
                    <option value="10k">10K</option>
                    <option value="half">Half Marathon</option>
                    <option value="marathon">Full Marathon</option>
                  </select>
                </div>
              </div>
              <div className="field">
                <label>Fitness Level</label>
                <div className="select-wrap">
                  <select value={level} onChange={e=>setLevel(e.target.value)}>
                    <option value="beginner">Beginner (0–20 km/wk)</option>
                    <option value="intermediate">Intermediate (20–50 km/wk)</option>
                    <option value="advanced">Advanced (50+ km/wk)</option>
                  </select>
                </div>
              </div>
              <div className="field full">
                <label>Training days per week</label>
                <div className="toggle-group">
                  {["3","4","5","6"].map(d=>(
                    <button key={d} className={`tog ${days===d?"on":""}`} onClick={()=>setDays(d)}>{d} days</button>
                  ))}
                </div>
              </div>
              <div className="field">
                <label>Goal time — hh</label>
                <input type="number" min="0" max="9" value={goalH} onChange={e=>setGoalH(e.target.value)}/>
              </div>
              <div className="field">
                <label>Goal time — mm</label>
                <input type="number" min="0" max="59" value={goalM} onChange={e=>setGoalM(e.target.value)}/>
              </div>
            </div>
 
            <button className="gen-btn" onClick={handleGenerate} disabled={loading}>
              {loading?"BUILDING YOUR PLAN…":"GENERATE MY PLAN →"}
            </button>
 
            {loading&&(
              <div className="loading-wrap">
                <div className="loading-label">Analysing your inputs…</div>
                <div className="lbar"><div className="lbar-inner"/></div>
              </div>
            )}
 
            {plan&&!loading&&(
              <div className="plan" style={{marginTop:48}}>
                <div className="plan-header">
                  <div>
                    <div className="sec-eyebrow" style={{marginBottom:6}}>Your plan</div>
                    <div className="plan-title">{RACE_LABELS[race]} — {WEEKS_FOR_RACE[race]} Weeks</div>
                  </div>
                  <div className="pills">
                    <div className="pill"><strong>{level}</strong></div>
                    <div className="pill">Goal <strong>{goalH}:{String(goalM).padStart(2,"0")}</strong></div>
                    <div className="pill">{days} days<strong>/wk</strong></div>
                  </div>
                </div>
 
                <VolumeChart plan={plan}/>
 
                {plan.map((week,wi)=>{
                  const pct = weekProgress(wi);
                  return(
                    <div className="week-card" key={wi}>
                      <div className="week-hdr" onClick={()=>toggleWeek(wi)}>
                        <div className="week-left">
                          <span className="wlabel">Week {week.week}</span>
                          <span className={`wtag ${week.type}`}>{week.type}</span>
                          {pct===100&&<span className="wcheck">✓ Done</span>}
                        </div>
                        <div className="week-right">
                          <span className="wkm">{week.totalKm} km</span>
                          {pct>0&&pct<100&&<span style={{fontSize:11,color:"var(--orange)"}}>{pct}%</span>}
                          <span className={`chevron ${expanded[wi]?"open":""}`}>↓</span>
                        </div>
                      </div>
 
                      {expanded[wi]&&(
                        <div className="week-body">
                          {pct>0&&(
                            <div className="progress-bar-wrap" style={{marginBottom:16}}>
                              <div className="pbar-label"><span>Progress</span><span>{pct}%</span></div>
                              <div className="pbar"><div className="pbar-fill" style={{width:`${pct}%`}}/></div>
                            </div>
                          )}
                          {week.days.map((d,di)=>(
                            <div className="day-row" key={di}>
                              <span className="dname">{d.day}</span>
                              <span className={`dwkt ${d.rest?"drest":""}`}>{d.workout}</span>
                              {!d.rest&&<span className="dkm">{d.km} km</span>}
                              {d.rest&&<span/>}
                              {!d.rest?(
                                <button
                                  className={`done-btn ${done[`${wi}_${di}`]?"checked":""}`}
                                  onClick={()=>toggleDone(wi,di)}
                                  title="Mark done"
                                >
                                  {done[`${wi}_${di}`]?"✓":"○"}
                                </button>
                              ):<span/>}
                            </div>
                          ))}
                        </div>
                      )}
                    </div>
                  );
                })}
 
                <div className="divider"/>
                <div className="action-row">
                  <button className="act-btn" onClick={()=>window.print()}>↓ Print / Save PDF</button>
                  <button className="act-btn" onClick={()=>setTab("Pacing")}>⏱ Open Pacing Calc</button>
                  <button className="act-btn" onClick={()=>setTab("Tracker")}>📈 Log a Run</button>
                </div>
              </div>
            )}
          </>
        )}
 
        {/* ── TAB: PACING ── */}
        {tab==="Pacing"&&(
          <>
            <div className="sec-eyebrow">Pacing Calculator</div>
            <div className="sec-title">KNOW YOUR<br/>PACE.</div>
            <p className="sec-sub">Enter your goal time and race. Get your exact pace, splits, and training zones.</p>
 
            <div className="calc-grid">
              <div className="field">
                <label>Race</label>
                <div className="select-wrap">
                  <select value={pcRace} onChange={e=>setPcRace(e.target.value)}>
                    <option value="5k">5K</option>
                    <option value="10k">10K</option>
                    <option value="half">Half Marathon</option>
                    <option value="marathon">Full Marathon</option>
                  </select>
                </div>
              </div>
              <div className="field"/>
              <div className="field">
                <label>Goal Hours</label>
                <input type="number" min="0" max="9" value={pcH} onChange={e=>setPcH(e.target.value)}/>
              </div>
              <div className="field">
                <label>Goal Minutes</label>
                <input type="number" min="0" max="59" value={pcM} onChange={e=>setPcM(e.target.value)}/>
              </div>
            </div>
 
            {pcResult&&(
              <>
                <div className="result-card">
                  <div className="result-title">Race Pace & Splits</div>
                  <div className="result-row">
                    <span className="result-label">Target Pace</span>
                    <span className="result-val big">{pcResult.pace}</span>
                  </div>
                  {pcResult.splits.map((s,i)=>(
                    <div className="result-row" key={i}>
                      <span className="result-label">{s.label}</span>
                      <span className="result-val">{s.time}</span>
                    </div>
                  ))}
                </div>
 
                <div className="result-card" style={{marginTop:16}}>
                  <div className="result-title">Training Pace Zones</div>
                  {Object.entries(pcResult.zones).map(([z,p])=>(
                    <div className="result-row" key={z}>
                      <span className="result-label" style={{textTransform:"capitalize"}}>{z} Run</span>
                      <span className="result-val">{p}</span>
                    </div>
                  ))}
                </div>
              </>
            )}
          </>
        )}
 
        {/* ── TAB: PREDICTOR ── */}
        {tab==="Predictor"&&(
          <>
            <div className="sec-eyebrow">Race Predictor</div>
            <div className="sec-title">WHAT CAN<br/>YOU RUN?</div>
            <p className="sec-sub">Enter a recent race time and we'll predict your potential across every distance using the Riegel formula.</p>
 
            <div className="calc-grid">
              <div className="field">
                <label>Known Distance (km)</label>
                <input type="number" min="1" max="100" value={rpDist} onChange={e=>setRpDist(e.target.value)} placeholder="e.g. 10"/>
              </div>
              <div className="field"/>
              <div className="field">
                <label>Your Time — hh</label>
                <input type="number" min="0" max="9" value={rpH} onChange={e=>setRpH(e.target.value)}/>
              </div>
              <div className="field">
                <label>Your Time — mm</label>
                <input type="number" min="0" max="59" value={rpM} onChange={e=>setRpM(e.target.value)}/>
              </div>
            </div>
 
            {prResult&&(
              <div className="predict-result">
                {prResult.map(r=>(
                  <div className="pred-cell" key={r.race}>
                    <div className="pred-race">{r.label}</div>
                    <div className="pred-time">{r.time}</div>
                    <div className="pred-pace">{r.pace}</div>
                  </div>
                ))}
              </div>
            )}
          </>
        )}
 
        {/* ── TAB: TRACKER ── */}
        {tab==="Tracker"&&(
          <>
            <div className="sec-eyebrow">Run Tracker</div>
            <div className="sec-title">LOG YOUR<br/>MILES.</div>
            <p className="sec-sub">Record every session. Watch your training volume build over time.</p>
 
            {logs.length>0&&(
              <div className="stats-row">
                <div className="stat-card">
                  <div className="stat-label">Total Runs</div>
                  <div className="stat-val">{logs.length}<span className="stat-unit">runs</span></div>
                </div>
                <div className="stat-card">
                  <div className="stat-label">Total Volume</div>
                  <div className="stat-val">{totalLogKm.toFixed(1)}<span className="stat-unit">km</span></div>
                </div>
                <div className="stat-card">
                  <div className="stat-label">Avg per Run</div>
                  <div className="stat-val">{avgLogKm}<span className="stat-unit">km</span></div>
                </div>
              </div>
            )}
 
            <div className="log-form">
              <div className="field">
                <label>Date</label>
                <input type="text" placeholder="e.g. May 25" value={logDate} onChange={e=>setLogDate(e.target.value)}/>
              </div>
              <div className="field">
                <label>Distance (km)</label>
                <input type="number" min="0" step=".1" placeholder="e.g. 12.5" value={logKm} onChange={e=>setLogKm(e.target.value)}/>
              </div>
              <div className="field">
                <label>Notes</label>
                <input type="text" placeholder="Easy, tired, felt great…" value={logNote} onChange={e=>setLogNote(e.target.value)}/>
              </div>
              <button className="log-btn" onClick={addLog}>+ Log</button>
            </div>
 
            {logs.length===0?(
              <div className="tracker-empty">No runs logged yet. Add your first session above.</div>
            ):(
              <>
                {logs.length>=3&&(
                  <div className="chart-wrap" style={{marginBottom:24}}>
                    <div className="chart-title">Recent Volume</div>
                    <ResponsiveContainer width="100%" height={120}>
                      <AreaChart data={[...logs].reverse().slice(0,12).map((l,i)=>({i:i+1,km:l.km}))} margin={{top:4,right:4,left:-20,bottom:0}}>
                        <defs>
                          <linearGradient id="logGrad" x1="0" y1="0" x2="0" y2="1">
                            <stop offset="0%" stopColor="#4a9eff" stopOpacity={.3}/>
                            <stop offset="100%" stopColor="#4a9eff" stopOpacity={0}/>
                          </linearGradient>
                        </defs>
                        <XAxis dataKey="i" tick={{fontSize:10,fill:"#666"}} axisLine={false} tickLine={false}/>
                        <YAxis tick={{fontSize:10,fill:"#666"}} axisLine={false} tickLine={false}/>
                        <Tooltip content={({active,payload})=>active&&payload?.length?<div className="custom-tooltip"><div className="label">Run</div><div className="val" style={{color:"#4a9eff"}}>{payload[0].value} km</div></div>:null}/>
                        <Area type="monotone" dataKey="km" stroke="#4a9eff" strokeWidth={2} fill="url(#logGrad)" dot={{r:3,fill:"#4a9eff"}}/>
                      </AreaChart>
                    </ResponsiveContainer>
                  </div>
                )}
                <table className="log-table">
                  <thead>
                    <tr><th>Date</th><th>Distance</th><th>Notes</th><th></th></tr>
                  </thead>
                  <tbody>
                    {logs.map(l=>(
                      <tr key={l.id}>
                        <td>{l.date}</td>
                        <td style={{color:"var(--orange)",fontFamily:"'Bebas Neue',sans-serif",fontSize:18}}>{l.km} km</td>
                        <td style={{color:"var(--muted)"}}>{l.note||"—"}</td>
                        <td><button className="del-btn" onClick={()=>setLogs(p=>p.filter(x=>x.id!==l.id))}>×</button></td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </>
            )}
          </>
        )}
      </div>
    </>
  );
}
 
