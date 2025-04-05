// 전체 카지노 사이트 통합 버전 (React + Supabase 연동 + 슬롯, 블랙잭, 바카라)
import { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { createClient } from "@supabase/supabase-js";

const supabase = createClient("https://your-project-id.supabase.co", "your-anon-key");
const getRandomCard = () => Math.floor(Math.random() * 10) + 1;

export default function CasinoSite() {
  const [balance, setBalance] = useState(1000);
  const [amount, setAmount] = useState(0);
  const [loggedIn, setLoggedIn] = useState(false);
  const [username, setUsername] = useState("");
  const [inputName, setInputName] = useState("");
  const [view, setView] = useState("wallet");
  const [bjPlayer, setBjPlayer] = useState([]);
  const [bjDealer, setBjDealer] = useState([]);
  const [bjResult, setBjResult] = useState("");
  const [bjInGame, setBjInGame] = useState(false);
  const [baccaratResult, setBaccaratResult] = useState(null);
  const [baccaratCards, setBaccaratCards] = useState({ player: [], banker: [] });
  const [slotResult, setSlotResult] = useState(null);
  const slotSymbols = ["🍒", "🍋", "🔔", "🍀", "7️⃣"];

  const fetchBalance = async (name) => {
    const { data } = await supabase.from("users").select("balance").eq("username", name).single();
    if (data) setBalance(data.balance);
  };

  const updateBalance = async (newBalance) => {
    setBalance(newBalance);
    await supabase.from("users").upsert({ username, balance: newBalance });
  };

  const handleLogin = async () => {
    if (inputName) {
      setUsername(inputName);
      setLoggedIn(true);
      await fetchBalance(inputName);
    }
  };

  const handleDeposit = () => updateBalance(balance + Number(amount));
  const handleWithdraw = () => {
    if (balance >= amount) updateBalance(balance - Number(amount));
  };

  const playSlot = () => {
    const bet = 100;
    if (balance < bet) return;
    const spin = [0, 0, 0].map(() => Math.floor(Math.random() * slotSymbols.length));
    const reward = spin.every((v) => v === spin[0]) ? bet * 5 : 0;
    setSlotResult(spin.map(i => slotSymbols[i]).join(" | "));
    updateBalance(balance - bet + reward);
  };

  const startBlackjack = () => {
    if (balance < 300) return;
    setBjInGame(true);
    setBjPlayer([getRandomCard(), getRandomCard()]);
    setBjDealer([getRandomCard(), getRandomCard()]);
    updateBalance(balance - 300);
    setBjResult("");
  };

  const hit = () => setBjPlayer([...bjPlayer, getRandomCard()]);
  const stand = () => {
    let dealer = [...bjDealer];
    while (dealer.reduce((a, b) => a + b, 0) < 17) dealer.push(getRandomCard());
    const playerTotal = bjPlayer.reduce((a, b) => a + b, 0);
    const dealerTotal = dealer.reduce((a, b) => a + b, 0);
    let result = "";
    if (playerTotal > 21) result = "플레이어 버스트! 패배";
    else if (dealerTotal > 21 || playerTotal > dealerTotal) {
      result = "플레이어 승리!";
      updateBalance(balance + 600);
    } else if (playerTotal === dealerTotal) {
      result = "무승부";
      updateBalance(balance + 300);
    } else result = "딜러 승리!";
    setBjResult(result);
    setBjDealer(dealer);
    setBjInGame(false);
  };

  const playBaccarat = () => {
    const bet = 300;
    if (balance < bet) return;

    const player = [getRandomCard(), getRandomCard()];
    const banker = [getRandomCard(), getRandomCard()];
    const playerTotal = (player[0] + player[1]) % 10;
    const bankerTotal = (banker[0] + banker[1]) % 10;
    const winner = playerTotal > bankerTotal ? "플레이어 승!" : playerTotal < bankerTotal ? "뱅커 승!" : "무승부!";

    let reward = 0;
    if (winner === "플레이어 승!") reward = bet;
    else if (winner === "뱅커 승!") reward = bet * 0.95;

    updateBalance(balance - bet + reward);
    setBaccaratResult(winner);
    setBaccaratCards({ player, banker });
  };

  if (!loggedIn) {
    return (
      <div className="min-h-screen flex items-center justify-center bg-gradient-to-br from-purple-900 to-black text-white">
        <Card className="p-6 w-full max-w-sm">
          <h2 className="text-2xl font-bold mb-4">🎰 로그인</h2>
          <Input placeholder="사용자 이름 입력" value={inputName} onChange={(e) => setInputName(e.target.value)} className="mb-4" />
          <Button onClick={handleLogin} className="w-full bg-yellow-400 hover:bg-yellow-300 text-black">입장하기</Button>
        </Card>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-900 to-black text-white p-6">
      <h1 className="text-4xl font-bold mb-6">💎 {username}님의 카지노</h1>
      <div className="flex gap-4 mb-6">
        <Button onClick={() => setView("wallet")} className="bg-gray-700">지갑</Button>
        <Button onClick={() => setView("slot")} className="bg-blue-700">슬롯</Button>
        <Button onClick={() => setView("blackjack")} className="bg-green-700">블랙잭</Button>
        <Button onClick={() => setView("baccarat")} className="bg-red-700">바카라</Button>
      </div>

      {view === "wallet" && (
        <Card className="max-w-md mx-auto">
          <CardContent className="p-6">
            <p className="text-lg mb-4">💰 현재 잔고: <span className="font-bold text-green-400">{balance.toLocaleString()} 코인</span></p>
            <Input type="number" placeholder="금액 입력" value={amount} onChange={(e) => setAmount(Number(e.target.value))} className="mb-4" />
            <div className="flex gap-4">
              <Button onClick={handleDeposit} className="bg-blue-500 hover:bg-blue-400">입금</Button>
              <Button onClick={handleWithdraw} className="bg-red-500 hover:bg-red-400">출금</Button>
            </div>
          </CardContent>
        </Card>
      )}

      {view === "slot" && (
        <Card className="max-w-md mx-auto text-center">
          <CardContent className="p-6">
            <h2 className="text-2xl font-bold mb-4">🎰 슬롯머신</h2>
            <div className="text-4xl mb-4">{slotResult || "🎰 🎰 🎰"}</div>
            <Button onClick={playSlot} className="bg-yellow-500 hover:bg-yellow-400">100코인 베팅</Button>
          </CardContent>
        </Card>
      )}

      {view === "blackjack" && (
        <Card className="max-w-md mx-auto">
          <CardContent className="p-6">
            <h2 className="text-2xl font-bold mb-4">🃏 블랙잭</h2>
            {!bjInGame && <Button onClick={startBlackjack} className="bg-green-500 hover:bg-green-400 mb-4">300코인 베팅 시작</Button>}
            {bjInGame && (
              <div className="mb-4">
                <p className="mb-2">플레이어 카드: {bjPlayer.join(", ")} (합: {bjPlayer.reduce((a, b) => a + b, 0)})</p>
                <div className="flex gap-2">
                  <Button onClick={hit} className="bg-blue-500 hover:bg-blue-400">히트</Button>
                  <Button onClick={stand} className="bg-yellow-500 hover:bg-yellow-400">스탠드</Button>
                </div>
              </div>
            )}
            {bjResult && (
              <div>
                <p className="font-bold mb-2">결과: {bjResult}</p>
                <p>딜러 카드: {bjDealer.join(", ")} (합: {bjDealer.reduce((a, b) => a + b, 0)})</p>
              </div>
            )}
          </CardContent>
        </Card>
      )}

      {view === "baccarat" && (
        <Card className="max-w-md mx-auto">
          <CardContent className="p-6">
            <h2 className="text-2xl font-bold mb-4">🂡 바카라</h2>
            <Button onClick={playBaccarat} className="bg-red-500 hover:bg-red-400 mb-4">300코인 베팅</Button>
            {baccaratResult && (
              <div>
                <p className="text-lg font-bold mb-2">결과: {baccaratResult}</p>
                <p className="text-blue-400">플레이어 카드: {baccaratCards.player.join(", ")} / 합: {(baccaratCards.player.reduce((a, b) => a + b) % 10)}</p>
                <p className="text-red-400">뱅커 카드: {baccaratCards.banker.join(", ")} / 합: {(baccaratCards.banker.reduce((a, b) => a + b) % 10)}</p>
              </div>
            )}
          </CardContent>
        </Card>
      )}
    </div>
  );
}
