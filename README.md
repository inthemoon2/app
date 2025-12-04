import React, { useState, useRef } from 'react';
import { Monitor, Smartphone, Link as LinkIcon, Image as ImageIcon, Users, ExternalLink, Download } from 'lucide-react';

export default function ClassProjectShowcase() {
  // 현재 선택된 반 (1반 ~ 7반)
  const [activeClass, setActiveClass] = useState(1);
  const fileInputRefs = useRef({});

  // 7개 반, 각 반당 7개 조의 데이터 초기화
  const [classesData, setClassesData] = useState(() => {
    const initialData = {};
    for (let c = 1; c <= 7; c++) {
      initialData[c] = Array.from({ length: 7 }, (_, i) => ({
        id: `class-${c}-team-${i + 1}`,
        teamNumber: i + 1,
        companyName: '',
        appTitle: '',
        appLink: '',
        posterImage: null, // base64 string for preview
      }));
    }
    return initialData;
  });

  // 입력 핸들러
  const handleInputChange = (teamId, field, value) => {
    setClassesData(prev => ({
      ...prev,
      [activeClass]: prev[activeClass].map(team => 
        team.id === teamId ? { ...team, [field]: value } : team
      )
    }));
  };

  // 이미지 업로드 핸들러 (로컬 미리보기용)
  const handleImageUpload = (teamId, e) => {
    const file = e.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onloadend = () => {
        handleInputChange(teamId, 'posterImage', reader.result);
      };
      reader.readAsDataURL(file);
    }
  };

  // 이미지 삭제
  const removeImage = (teamId) => {
    handleInputChange(teamId, 'posterImage', null);
    if (fileInputRefs.current[teamId]) {
      fileInputRefs.current[teamId].value = '';
    }
  };

  const activeTeams = classesData[activeClass];

  return (
    <div className="min-h-screen bg-gray-100 font-sans text-gray-800">
      
      {/* 헤더 */}
      <header className="bg-gradient-to-r from-blue-600 to-indigo-700 text-white shadow-lg sticky top-0 z-20">
        <div className="max-w-7xl mx-auto px-4 py-4 flex flex-col md:flex-row items-center justify-between gap-4">
          <div className="flex items-center gap-3">
            <div className="p-2 bg-white/20 rounded-lg">
              <Monitor className="w-6 h-6" />
            </div>
            <div>
              <h1 className="text-xl font-bold tracking-tight">2025학년도 SW 스타트업 프로젝트</h1>
              <p className="text-xs text-blue-100">학생 주도 소프트웨어 스타트업 설립 및 앱 개발 프로젝트</p>
            </div>
          </div>
          
          {/* 반 선택 탭 */}
          <div className="flex overflow-x-auto gap-2 pb-1 w-full md:w-auto scrollbar-hide">
            {[1, 2, 3, 4, 5, 6, 7].map((num) => (
              <button
                key={num}
                onClick={() => setActiveClass(num)}
                className={`
                  flex-shrink-0 px-4 py-2 rounded-full text-sm font-bold transition-all
                  ${activeClass === num 
                    ? 'bg-white text-blue-700 shadow-md scale-105' 
                    : 'bg-blue-800/40 text-blue-100 hover:bg-blue-700/50'}
                `}
              >
                {num}반
              </button>
            ))}
          </div>
        </div>
      </header>

      <main className="max-w-7xl mx-auto p-4 md:p-6">
        
        <div className="flex items-center justify-between mb-6">
          <h2 className="text-2xl font-bold text-gray-800 flex items-center gap-2">
            <span className="bg-indigo-100 text-indigo-700 px-3 py-1 rounded-lg text-lg">
              {activeClass}반
            </span>
            <span className="text-base text-gray-500 font-normal">
              총 7개 스타트업(조)
            </span>
          </h2>
          <div className="text-sm text-gray-500 bg-white px-3 py-1 rounded-full border shadow-sm hidden md:block">
            ※ 각 카드를 클릭하여 내용을 수정하세요
          </div>
        </div>

        {/* 7개 조 그리드 레이아웃 */}
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
          {activeTeams.map((team) => (
            <div 
              key={team.id}
              className="group bg-white rounded-2xl shadow-sm border border-gray-200 hover:shadow-xl hover:border-blue-300 transition-all duration-300 flex flex-col overflow-hidden"
            >
              {/* 카드 헤더: 앱 제목 중심 */}
              <div className="bg-white pt-5 pb-3 px-4 border-b border-gray-100 flex flex-col items-center relative">
                
                {/* 조 번호 (작게 표시) */}
                <span className="text-xs font-bold text-indigo-500 bg-indigo-50 px-2 py-0.5 rounded-full mb-1 flex items-center gap-1">
                  <Users size={10} /> {team.teamNumber}조
                </span>

                {/* 앱 제목 입력 (메인) */}
                <input
                  type="text"
                  value={team.appTitle}
                  onChange={(e) => handleInputChange(team.id, 'appTitle', e.target.value)}
                  placeholder="앱 제목 입력"
                  className="w-full text-center font-bold text-xl text-gray-900 placeholder-gray-300 border-b-2 border-transparent hover:border-gray-200 focus:border-indigo-500 focus:outline-none transition-colors bg-transparent px-1 py-1"
                />

                {/* 링크 아이콘 (우측 상단 절대 배치) */}
                {team.appLink && (
                  <a 
                    href={team.appLink} 
                    target="_blank" 
                    rel="noopener noreferrer"
                    className="absolute top-3 right-3 text-gray-400 hover:text-blue-600 transition-colors p-1"
                    title="앱 보러가기"
                  >
                    <ExternalLink size={16} />
                  </a>
                )}
              </div>

              {/* 포스터 이미지 영역 */}
              <div className="relative aspect-[3/4] bg-gray-50 w-full group/image overflow-hidden border-b border-gray-100">
                {team.posterImage ? (
                  <>
                    <img 
                      src={team.posterImage} 
                      alt={`${team.teamNumber}조 포스터`} 
                      className="w-full h-full object-cover transition-transform duration-700 group-hover:scale-105"
                    />
                    <button 
                      onClick={() => removeImage(team.id)}
                      className="absolute top-2 right-2 bg-black/50 hover:bg-red-500 text-white p-1.5 rounded-full opacity-0 group-hover/image:opacity-100 transition-all backdrop-blur-sm"
                      title="포스터 삭제"
                    >
                      <Download className="w-4 h-4 rotate-45" />
                    </button>
                  </>
                ) : (
                  <label className="flex flex-col items-center justify-center w-full h-full cursor-pointer hover:bg-gray-100 transition-colors text-gray-400 group/upload">
                    <div className="bg-white p-4 rounded-full shadow-sm mb-3 group-hover/upload:scale-110 transition-transform">
                        <ImageIcon size={32} className="text-indigo-200" />
                    </div>
                    <span className="text-sm font-medium text-gray-500">포스터 등록</span>
                    <input 
                      type="file" 
                      accept="image/*"
                      className="hidden"
                      ref={el => fileInputRefs.current[team.id] = el}
                      onChange={(e) => handleImageUpload(team.id, e)}
                    />
                  </label>
                )}
              </div>

              {/* 하단 정보 영역: 회사 이름 및 링크 */}
              <div className="p-4 bg-gray-50/50 flex-1 flex flex-col justify-end gap-3">
                
                {/* 회사 이름 */}
                <div className="space-y-1">
                  <label className="text-[10px] font-bold text-gray-400 uppercase tracking-wider pl-1">Company</label>
                  <input
                    type="text"
                    value={team.companyName}
                    onChange={(e) => handleInputChange(team.id, 'companyName', e.target.value)}
                    placeholder="회사명을 입력하세요"
                    className="w-full font-semibold text-md text-gray-700 placeholder-gray-300 bg-white border border-gray-200 rounded-lg px-3 py-2 focus:ring-2 focus:ring-indigo-100 focus:border-indigo-400 focus:outline-none transition-all"
                  />
                </div>

                {/* 링크 입력 */}
                <div className="relative">
                  <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                    <LinkIcon size={12} className="text-gray-400" />
                  </div>
                  <input
                    type="url"
                    value={team.appLink}
                    onChange={(e) => handleInputChange(team.id, 'appLink', e.target.value)}
                    placeholder="https://앱링크.com"
                    className="w-full text-xs text-blue-600 placeholder-gray-400 bg-white border border-gray-200 rounded-lg pl-8 pr-3 py-2 focus:ring-2 focus:ring-blue-100 focus:border-blue-400 focus:outline-none transition-all"
                  />
                </div>
              </div>

            </div>
          ))}
        </div>

        {/* 하단 안내 */}
        <div className="mt-12 text-center text-gray-400 text-sm pb-8">
          <p>© 2025 {activeClass}반 SW 프로젝트 발표회</p>
        </div>
      </main>

      <style jsx global>{`
        .scrollbar-hide::-webkit-scrollbar {
            display: none;
        }
        .scrollbar-hide {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }
      `}</style>
    </div>
  );
}
