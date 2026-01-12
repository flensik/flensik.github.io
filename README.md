import React, { useState, useEffect } from 'react';
import { Gift, Sparkles, ChevronRight } from 'lucide-react';

const skins = {
  legendary: [
    { name: 'AK-47 | Огненный змей', rarity: 'legendary', image: 'https://images.unsplash.com/photo-1595590424283-b8f17842773f?w=300&h=200&fit=crop', chance: 0.5 },
    { name: 'AWP | Драконья ярость', rarity: 'legendary', image: 'https://images.unsplash.com/photo-1511593358241-7eea1f3c84e5?w=300&h=200&fit=crop', chance: 0.5 }
  ],
  epic: [
    { name: 'M4A1 | Неоновый гонщик', rarity: 'epic', image: 'https://images.unsplash.com/photo-1509664158680-07c5032b51e5?w=300&h=200&fit=crop', chance: 2 },
    { name: 'Desert Eagle | Кобра', rarity: 'epic', image: 'https://images.unsplash.com/photo-1583573607873-4336a04c198f?w=300&h=200&fit=crop', chance: 2 },
    { name: 'Glock-18 | Водная стихия', rarity: 'epic', image: 'https://images.unsplash.com/photo-1526554850534-7c78330d5f90?w=300&h=200&fit=crop', chance: 2 }
  ],
  rare: [
    { name: 'AK-47 | Красная линия', rarity: 'rare', image: 'https://images.unsplash.com/photo-1592103862613-191ec9ba0c19?w=300&h=200&fit=crop', chance: 8 },
    { name: 'USP-S | Орион', rarity: 'rare', image: 'https://images.unsplash.com/photo-1580193769210-b8d1c049a7d9?w=300&h=200&fit=crop', chance: 8 },
    { name: 'P90 | Азимов', rarity: 'rare', image: 'https://images.unsplash.com/photo-1593642532400-2682810df593?w=300&h=200&fit=crop', chance: 8 }
  ],
  common: [
    { name: 'MP5 | Песчаный камуфляж', rarity: 'common', image: 'https://images.unsplash.com/photo-1595433707802-6b2626ef1c91?w=300&h=200&fit=crop', chance: 20 },
    { name: 'FAMAS | Контраст', rarity: 'common', image: 'https://images.unsplash.com/photo-1584573122546-9a87f8b90ae3?w=300&h=200&fit=crop', chance: 20 },
    { name: 'MAC-10 | Градиент', rarity: 'common', image: 'https://images.unsplash.com/photo-1551269901-5c5e14c25df7?w=300&h=200&fit=crop', chance: 20 },
    { name: 'P250 | Вихрь', rarity: 'common', image: 'https://images.unsplash.com/photo-1582043786804-768c28c19c0a?w=300&h=200&fit=crop', chance: 19.5 }
  ]
};

const allSkins = [...skins.legendary, ...skins.epic, ...skins.rare, ...skins.common];

const rarityColors = {
  legendary: 'from-yellow-500 via-orange-500 to-red-500',
  epic: 'from-purple-500 via-pink-500 to-purple-600',
  rare: 'from-blue-500 via-cyan-500 to-blue-600',
  common: 'from-gray-400 via-gray-500 to-gray-600'
};

const rarityNames = {
  legendary: 'Легендарный',
  epic: 'Эпический',
  rare: 'Редкий',
  common: 'Обычный'
};

export default function CaseOpener() {
  const [balance, setBalance] = useState(1000);
  const [isOpening, setIsOpening] = useState(false);
  const [wonSkin, setWonSkin] = useState(null);
  const [inventory, setInventory] = useState([]);
  const [showInventory, setShowInventory] = useState(false);
  const [rouletteItems, setRouletteItems] = useState([]);
  const [animationOffset, setAnimationOffset] = useState(0);

  const casePrice = 100;

  const getRandomSkin = () => {
    const rand = Math.random() * 100;
    let cumulative = 0;
    
    for (const skin of allSkins) {
      cumulative += skin.chance;
      if (rand <= cumulative) {
        return skin;
      }
    }
    return allSkins[allSkins.length - 1];
  };

  const openCase = () => {
    if (balance < casePrice || isOpening) return;
    
    setBalance(balance - casePrice);
    setIsOpening(true);
    setWonSkin(null);

    // Генерируем список предметов для рулетки
    const items = [];
    for (let i = 0; i < 50; i++) {
      items.push(getRandomSkin());
    }
    
    const winningIndex = 45;
    const winner = getRandomSkin();
    items[winningIndex] = winner;
    
    setRouletteItems(items);
    
    // Анимация
    const targetOffset = -(winningIndex * 180 - window.innerWidth / 2 + 90);
    setAnimationOffset(0);
    
    setTimeout(() => {
      setAnimationOffset(targetOffset);
    }, 50);

    setTimeout(() => {
      setWonSkin(winner);
      setInventory([...inventory, winner]);
      setIsOpening(false);
    }, 5000);
  };

  const addFunds = () => {
    setBalance(balance + 500);
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-900 via-purple-900 to-gray-900 text-white p-4">
      <div className="max-w-4xl mx-auto">
        {/* Шапка */}
        <div className="bg-black/40 backdrop-blur rounded-xl p-6 mb-6 border border-purple-500/30">
          <div className="flex justify-between items-center">
            <div>
              <h1 className="text-3xl font-bold bg-gradient-to-r from-yellow-400 to-orange-500 bg-clip-text text-transparent">
                Standoff 2 Cases
              </h1>
              <p className="text-gray-400 mt-1">Открывай кейсы и получай легендарные скины</p>
            </div>
            <div className="text-right">
              <p className="text-sm text-gray-400">Баланс</p>
              <p className="text-2xl font-bold text-yellow-400">{balance} ₽</p>
              <button
                onClick={addFunds}
                className="mt-2 px-4 py-1 bg-green-600 hover:bg-green-700 rounded text-sm transition"
              >
                + 500 ₽
              </button>
            </div>
          </div>
        </div>

        {/* Кейс */}
        <div className="bg-black/40 backdrop-blur rounded-xl p-8 mb-6 border border-purple-500/30">
          <div className="text-center mb-6">
            <Gift className="w-24 h-24 mx-auto mb-4 text-purple-400" />
            <h2 className="text-2xl font-bold mb-2">Элитный кейс</h2>
            <p className="text-gray-400">Цена: {casePrice} ₽</p>
          </div>

          {/* Рулетка */}
          <div className="relative h-48 mb-6 overflow-hidden rounded-lg bg-black/60">
            <div className="absolute top-1/2 left-1/2 w-1 h-full bg-yellow-400 z-10 transform -translate-x-1/2"></div>
            
            <div 
              className="flex absolute top-1/2 transform -translate-y-1/2 transition-all duration-[5000ms] ease-out"
              style={{ 
                transform: `translateX(${animationOffset}px) translateY(-50%)`,
                transitionTimingFunction: 'cubic-bezier(0.22, 0.61, 0.36, 1)'
              }}
            >
              {rouletteItems.map((skin, idx) => (
                <div
                  key={idx}
                  className="flex-shrink-0 w-40 h-40 mx-2 rounded-lg overflow-hidden border-2"
                  style={{ borderColor: `var(--${skin.rarity})` }}
                >
                  <div className={`w-full h-full bg-gradient-to-br ${rarityColors[skin.rarity]} p-1`}>
                    <div className="bg-black/80 w-full h-full rounded flex flex-col items-center justify-center p-2">
                      <img 
                        src={skin.image} 
                        alt={skin.name}
                        className="w-24 h-16 object-cover rounded mb-2"
                      />
                      <p className="text-xs text-center font-semibold">{skin.name}</p>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </div>

          {/* Кнопка открытия */}
          <button
            onClick={openCase}
            disabled={balance < casePrice || isOpening}
            className={`w-full py-4 rounded-lg font-bold text-lg transition-all transform hover:scale-105 ${
              balance < casePrice || isOpening
                ? 'bg-gray-600 cursor-not-allowed'
                : 'bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-700 hover:to-pink-700'
            }`}
          >
            {isOpening ? (
              <span className="flex items-center justify-center">
                <Sparkles className="animate-spin mr-2" />
                Открываем...
              </span>
            ) : balance < casePrice ? (
              'Недостаточно средств'
            ) : (
              `Открыть за ${casePrice} ₽`
            )}
          </button>
        </div>

        {/* Выигрыш */}
        {wonSkin && (
          <div className="bg-black/40 backdrop-blur rounded-xl p-6 mb-6 border-2 animate-pulse"
               style={{ borderColor: `var(--${wonSkin.rarity})` }}>
            <h3 className="text-2xl font-bold text-center mb-4">🎉 Поздравляем!</h3>
            <div className={`bg-gradient-to-br ${rarityColors[wonSkin.rarity]} p-1 rounded-lg`}>
              <div className="bg-black/90 p-6 rounded-lg">
                <img 
                  src={wonSkin.image} 
                  alt={wonSkin.name}
                  className="w-full h-48 object-cover rounded-lg mb-4"
                />
                <p className="text-xl font-bold text-center mb-2">{wonSkin.name}</p>
                <p className="text-center text-lg">{rarityNames[wonSkin.rarity]}</p>
              </div>
            </div>
          </div>
        )}

        {/* Инвентарь */}
        <div className="bg-black/40 backdrop-blur rounded-xl p-6 border border-purple-500/30">
          <button
            onClick={() => setShowInventory(!showInventory)}
            className="w-full flex items-center justify-between mb-4"
          >
            <h3 className="text-xl font-bold">Инвентарь ({inventory.length})</h3>
            <ChevronRight className={`transform transition-transform ${showInventory ? 'rotate-90' : ''}`} />
          </button>
          
          {showInventory && (
            <div className="grid grid-cols-2 md:grid-cols-3 gap-4">
              {inventory.map((skin, idx) => (
                <div
                  key={idx}
                  className={`bg-gradient-to-br ${rarityColors[skin.rarity]} p-0.5 rounded-lg`}
                >
                  <div className="bg-black/90 p-3 rounded-lg">
                    <img 
                      src={skin.image} 
                      alt={skin.name}
                      className="w-full h-24 object-cover rounded mb-2"
                    />
                    <p className="text-sm font-semibold">{skin.name}</p>
                    <p className="text-xs text-gray-400">{rarityNames[skin.rarity]}</p>
                  </div>
                </div>
              ))}
            </div>
          )}
        </div>
      </div>
    </div>
  );
}
