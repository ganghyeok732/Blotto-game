import React, { useState } from "react";
import {
  Box,
  Button,
  Text,
  Grid,
  GridItem,
  VStack,
  Input
} from "@chakra-ui/react";

export default function Home() {
  const MAX_TROOPS = 100;
  const NUM_FIELDS = 5;

  const [troops, setTroops] = useState(Array(NUM_FIELDS).fill(0));
  const [opponent, setOpponent] = useState(Array(NUM_FIELDS).fill(0));
  const [error, setError] = useState("");
  const [result, setResult] = useState("");

  const totalTroops = troops.reduce((a, b) => a + b, 0);

  const handleChange = (index, value) => {
    const num = parseInt(value) || 0;
    const newTroops = [...troops];
    newTroops[index] = num;

    const total = newTroops.reduce((a, b) => a + b, 0);
    if (total > MAX_TROOPS) {
      setError("총 병력은 100명을 넘을 수 없습니다!");
    } else {
      setError("");
    }

    setTroops(newTroops);
    setResult(""); // 변경 시 결과 초기화
  };

  const resetBoard = () => {
    setTroops(Array(NUM_FIELDS).fill(0));
    setOpponent(Array(NUM_FIELDS).fill(0));
    setError("");
    setResult("");
  };

  const generateOpponent = () => {
    let remaining = 100;
    const dist = Array(NUM_FIELDS)
      .fill(0)
      .map((_, i) => {
        if (i === NUM_FIELDS - 1) return remaining;
        const max = remaining - (NUM_FIELDS - 1 - i);
        const val = Math.floor(Math.random() * (max + 1));
        remaining -= val;
        return val;
      });
    return dist;
  };

  const confirmTroops = () => {
    if (totalTroops !== MAX_TROOPS) {
      setError("총 병력을 정확히 100명 배치해야 합니다.");
      return;
    }

    const opp = generateOpponent();
    setOpponent(opp);

    let win = 0, lose = 0;
    for (let i = 0; i < NUM_FIELDS; i++) {
      if (troops[i] > opp[i]) win++;
      else if (troops[i] < opp[i]) lose++;
    }

    if (win > lose) setResult("🎉 승리!");
    else if (win < lose) setResult("😢 패배...");
    else setResult("🤝 무승부");

    setError("");
  };

  return (
    <VStack spacing={4} p={4}>
      <Text fontSize="2xl" fontWeight="bold">
        블로토 병력 배치
      </Text>

      <Text fontSize="md" color="gray.600">
        총 병력을 100명 안에서 배치하세요
      </Text>

      <Text fontSize="md" color="blue.400">
        현재 배치된 병력: {totalTroops}명
      </Text>

      {error && <Text color="red.500">{error}</Text>}

      {/* 병력 입력 */}
      <Grid templateColumns="repeat(5, 1fr)" gap={3}>
        {troops.map((count, i) => (
          <GridItem key={i}>
            <Text textAlign="center" mb={1}>전장 {i + 1}</Text>
            <Input
              type="number"
              min="0"
              value={count}
              onChange={(e) => handleChange(i, e.target.value)}
            />
            <Text fontSize="sm" color="gray.500" textAlign="center">
              상대: {opponent[i]}
            </Text>
          </GridItem>
        ))}
      </Grid>

      {/* 버튼 */}
      <Box>
        <Button colorScheme="teal" mr={2} onClick={confirmTroops}>
          배치 완료
        </Button>
        <Button colorScheme="red" onClick={resetBoard}>
          초기화
        </Button>
      </Box>

      {/* 결과 메시지 */}
      {result && (
        <Text fontSize="xl" fontWeight="bold" color="teal.400">
          결과: {result}
        </Text>
      )}
    </VStack>
  );
}
